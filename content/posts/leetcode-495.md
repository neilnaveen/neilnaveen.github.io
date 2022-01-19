---
title: "Leetcode-495"
date: 2021-01-19T11:28:06-05:00
toc: false
images:
tags : [leetcode,math,golang ]
---
[495. Teemo Attacking](https://leetcode.com/problems/teemo-attacking/)

In this problem, we focus on that teemo's attack duration is reset when teemo attacks again.

The code finds if teemo's attacks overlap with the next attack. If it does, add the duration that the attack lasts before it is reset `- 1`.  If not, add the duration to the result.

```go
func findPoisonedDuration(timeSeries []int, duration int) int {
    res := 0
    for i := 0 ; i < len(timeSeries)-1 ; i++{
        if timeSeries[i] + duration-1 >= timeSeries[i+1]{
            res += timeSeries[i+1] - timeSeries[i]
        }else{
            res += duration
        }
    }
    res += duration
    return res
}
```
