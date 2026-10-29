---
title: "Speeding up eBPF filepath checks"
date: 2026-10-28T14:42:00-05:00
toc: false
images:
tags: [ebpf]
---

Recently, I was testing our eBPF agent, and I found a major bottleneck in our code. My brother Nathan solved half the problem by [caching the results](https://nathannaveen.dev/posts/dropping-ebpf-cpu-cost-by-90/). This blog post covers how we speed up the limiting factor itself, filepath checking, even when we don't have cached results.

Before we dive into the performance changes, I want to briefly explain what our eBPF agent does so everything makes sense. Essentially, the eBPF agent receives a list of protected directories, if someone tries to mutate any files under those directories, we apply our policy. This is a gross simplification, so for a more in-depth one, take a look at the [bomfather/agent README](https://github.com/bomfather/agent).

We recently open sourced our agent, so you can find everything discussed in this blog post at [github.com/bomfather/agent](https://github.com/bomfather/agent).

The changes I talk about in this blog post made our agent about 5.3x faster.

## What even is the problem?

Our agent processes file opens through a 3-step process:

1. First, it creates a string of the entire filepath (in eBPF, we don’t get full filepaths directly, we must construct them from objects called dentry’s).
2. We iterate through the filepath’s entire length, which is up to 1024 bytes, and in each iteration we take the prefix of the filepath up to the current index, hash it, and query a map with it. We do this to check if this filepath is under any of the protected directories.
3. The program then decides whether to allow the file to open.

In this process, most of the time is spent in step 2. Step 1 is quite fast because constructing our filepath involves iterating through each layer of the filesystem, from the current file to its parent directory and so on, this step doesn’t take as long as iterating through the whole filepath.

If we look at this problem closely, we can reframe it as a prefix matching problem. And one of the best ways to solve prefix matching is with tries!

Tries reduce the time from O(N) to O(P), where N is the full filepath length, and P is the longest prefix length.

Conveniently, eBPF has its own built in trie, the eBPF LPM Trie, with efficiencies we couldn’t achieve with kernel memory optimizations, and the algorithm itself is faster than a normal trie. A normal trie still has to iterate through every character of a prefix to match a given input, but the eBPF LPM trie chunks text, so it needs far fewer iterations.

This approach has a problem, our file paths can be up to 1024 bytes long, but the eBPF LPM trie can only take up to 256 bytes. This means we either can't use the eBPF Trie, or we have to condense our filepaths while preserving their meaning, from 1024 bytes down to 256 bytes. It can’t be that hard to shorten a file path? Right?

## Chunk-chaining

For a second, let's forget about tries, pattern matching, the entire problem, and zoom in on a single subproblem:

Given an input string and a list of prefixes, what is the most efficient way to reduce a large portion of the string's prefix to a single character? This number must be correlatable to one or more elements in the list of prefixes. Finally, we are okay with a long preprocessing phase, we just want the result to be computed as fast as possible when we pass in a new input string.

A naive approach would be to assign each prefix in this list a unique ID, then iterate through the input string, constantly checking whether the substring from 0 to N (N being the current index) matches a prefix, and, if so, replace it with its corresponding ID. This is essentially what we were doing at the start.

But remember, our requirements say we can spend as much time as we want in the preprocessing phase, so how can we speed this up by doing some calculations beforehand?

Instead of assigning a single ID to each prefix, what if we choose a chunk size, split each prefix into chunks of that size, and assign an ID to each chunk?

Now we could represent a prefix with a list of IDs plus some extra text that isn’t enough to fill a chunk (example: 1 + 3 + 9 + “extratext”).

The magic happens when we receive an input string. Instead of iterating through the string, we jump through it chunk by chunk, get the IDs, and replace each chunk with them. Since they are of fixed length, we don’t have to iterate through each character, so instead of querying a hash map with every single possible prefix in the input string, we just need to iterate N/C times, where N is the length of the input string, and C is the size of a chunk.

We can also condense the IDs so that, instead of having multiple IDs, we can represent all chunks with a single ID. If a chunk is made up not only of text but also of the current chunk’s text plus the previous chunk ID, we can create a linked-list-like structure, where each ID also represents the chunks before it as a single number.

![Chunk chaining, where each ID stands for that chunk and every chunk before it](/image.jpg)

But we are left with one problem: what do we do with the leftover text, the text that can’t be chunked?

Here, we must return to the original problem.

Remember how LPM tries in eBPF are limited to 256 bytes? Well, if we make chunks 128 bytes long, this is smaller than the 256-byte limit, and exactly 10 chunks make up the length of a filepath (our input string with a max length of 1024 bytes)

Imagine encoding all protected directories (prefixes) and the filepath using our chunk-chaining method, so each protected directory is represented as a number plus some extra leftover text with a length smaller than 128 bytes. We can also chunk the opened file path, representing it as a number plus extra leftover bytes.

If we put all these encoded protected directories into the eBPF LPM Trie and queried it with our encoded filepath string, we could match prefixes perfectly. This works because:

1. The trie first matches the chunk ID at the start of both encoded protected directories and the encoded filepath to find a match. This guarantees a match with the right protected directory, which is also built from that chunk ID.
2. The rest of the extra text in a prefix is now matched against the extra text in the filepath; if there were two items in the Trie that matched the chunk ID, whichever is the longest and also matches the prefix text with the filepath is returned. Essentially, this part uses the trie for what a trie is meant for.

This lets us use the eBPF LPM trie with at most 10 iterations (for chunking the filepath) plus one LPM lookup.

## Performance

![Flamegraph before the filepath check change](/OldPerf.png)

![Flamegraph after the filepath check change](/NewPerf.png)

To better understand these flamegraphs, we need a basic understanding of the eBPF agent's structure. We have a hook, `lsm_file_open`, and inside it a function called tail call security check (a tail call is a special type of function in eBPF), and under that, a function called `is_restricted_filepath`. This is the important function and what actually changed between the new and old performance graphs.

In the old flamegraph, `is_restricted_filepath` had a function under it called `path_check_callback`. Now, this function wasn't regular, it was essentially a loop that iterated over all 1024 characters of a string. Since eBPF cannot normally handle those long iterations, we can use a helper called `bpf_loop`, which calls a function like `path_check_callback` a specified number of times.

With these changes, we removed the entire BPF loop and replaced it with a simple for loop that runs at most 10 times to iterate through all chunks. Then it hits the LPM trie and returns its result. The chunking and querying logic is [here](https://github.com/bomfather/agent/blob/cfc7538af5f89b096e1ed0bae0d7bc23897c3229/bpf/trace.c#L1471-L1728).

With these speedups, on our test, without any caching, we made our lookups 5.3 times faster. We still have more optimization work to do, much of it involves analyzing the code itself and optimizing function calls and map lookups, but this change delivered such a large speedup because we changed the entire architecture of our lookups.

## Conclusion

This writeup is not really only about our security agent or our specific use case, the focus of this blog post was more on my thought process for speeding up our eBPF code, and hopefully, when you are writing your eBPF agents, this may help you look at some perf problems in a different way.

On a related note, after I solved this problem, I asked GPT Sol 6 how it would have solved it, and it immediately told me we could never use the trie because of its input requirements. Even after I nudged it a bit and asked how we could use the trie by modifying our data, it returned a mediocre solution that only slightly leveraged the trie and would take an order of magnitude longer than our current solution.

This surprised me because these models are really good at finding solutions for very scoped problems, like optimizations of data structures. I suspect it didn’t perform well because the documentation on the eBPF LPM trie specifically covered the trie's limits, and when our input length didn’t match the trie’s limit, it couldn’t think around those limits, which is still quite surprising.

Remember, our brains still have value, and until LLMs are truly and completely smarter than us, use them!
