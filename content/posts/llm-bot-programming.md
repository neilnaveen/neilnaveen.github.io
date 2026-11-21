---
title: "LLMs have made bot programming boring, how can we make it fun again?"
date: 2026-10-28T14:42:00-05:00
toc: false
images:
tags: [llms, programming]
---

If you’ve ever played codingame or any equivalent multiplayer bot programming contest, you know that it's one of the most fun ways to program. Bot programming is competitive programming where you code programs (bots) that compete against other people's bots to achieve some objective.

But in recent years, with the onset of capable LLMs, people have been spamming Codingame with auto generated LLM solutions that, frankly, work quite well. I don’t really like this, since I really enjoyed (and still enjoy) the struggle of creating solutions on Codingame. I don’t do bot programming to create some external value (resume, etc.), I do it for the struggle and the urge to beat other human players. And I assume that a lot of people who do bot programming do it for a similar reason.

Bot programming isn’t just about using algorithms to win, instead, it is about trying out different strategies to beat other players' strategies. And when people use AI to brute force your algorithm/strategy, it gets boring because there is no longer an incentive to create new solutions, since you know someone can copy it to a degree by just throwing tokens at the problem with little to no effort. 

Top human written, creative solutions still beat these LLMs, but automated LLMs in loops are reaching fairly high levels of play, making it annoying for anyone who isn’t god level. 

So, how can we change this imbalance?

## Who am I?

I am by no means one of the top players on Codingame or any other bot programming competition, but I have spent a large amount of my life playing these games on different platforms (I started when I was 8, and I am currently 18). This is just what I have observed and what I think will become of the entire bot programming space. 

Github: https://github.com/neilnaveen

Leetcode: https://leetcode.com/u/neilnaveen/

Codingame: https://www.codingame.com/profile/0fa733a2c7f92a829e4190625b5b9a485718854

## What is the difference between Codingame and Regular Coding Competions?

We first need to look at the difference between algorithmic competitions (Codeforces, LeetCode, etc.) and bot programming (Codingame). In Codeforces, there are a couple of problem statements, and the goal is to solve these problems as quickly as possible, there is only one metric, speed. 

On the other hand, in Codingame, people can use different strategies, and different strategies can counter each other; while there is a leaderboard, the ordering is asymmetric: A may beat B, and B may beat C, but C can beat A, which is impossible on a platform like Codeforces. 

This is why it is relatively harder for LLMs to solve Codingame contests than Codeforces contests, most Codeforces problems are quite similar to previous problems. At the same time, Codingame takes ingenuity to come up with solutions that don’t already exist. 

But LLMs find a way to solve Codingame competitions because, in the end, most top solutions gravitate towards one another. There is mostly one or two ways to be the best in each contest, which is mostly built on a couple of algorithms (MCTS, Flood Fill, DFS, BFS, etc.), and more importantly, this one algorithm runs 90% of the bot, so optimizing this algorithm gets you to the top.

## How can we make Bot Programming fun again?

I don’t know if Codingame can adapt in this way, but theoretically, how could Codingame or a Codingame equivalent fix this problem?

I think these games need more creativity, there need to be multiple ways to reach the winning condition. Even if the winning condition is static, there should be multiple ways of getting there. 

Just think about all modern FPS games, they have multiple ablities you can select, multiple guns you can play, and multiple ways of playing the game with those selected abilites. If that happens, humans can start coming up with different and out of the box solutions. 

From what I have seen, LLMs shine when there is a cookie cutter way to get to the solution, they will solve it in a way that may work now but probably won’t work later. If you have ever tried vibe coding any serious application, you will see it immediately overcomplicate solutions, account for problems that don’t exist, and ignore bad design choices that will lead to future problems. They love sticking to patterns they have seen before, so to make something that they can’t do well, we need to add uncertainty, just like the real world, to our games. I feel that is where we will see real advancement in people actually using their brains to solve the problem.

I think LLMs are very useful and quite remarkable, but I still think our human brains are very good at innovation and solving problems with unique, out of the box solutions. If we want a coding game that is actually challenging, we just need to make ones that emphasize unique, different solutions instead of rewarding only those that gravitate toward the obvious. 

