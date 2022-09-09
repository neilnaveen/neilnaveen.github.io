---
title: "Leetcode-1899"
date: 2022-09-03T11:28:06-05:00
toc: false
images:
tags : [leetcode,golang]
---
In this soultion we are looking at all the all the triplets that have values smaller than or equal to those in the target array, and merging them all into one triplet, `temp`.

In this way we will make the greatest triplet that is smaller than equal to the target triplet, which will be the closetst triplet we can make to target.

``` go
func mergeTriplets(triplets [][]int, target []int) bool {
    var temp [3]int
    
    for _, i := range triplets {
        if i[0] <= target[0] && i[1] <= target[1] && i[2] <= target[2] {
            temp = [3]int{max(i[0],temp[0]), max(i[1],temp[1]), max(i[2], temp[2])}
        } 
    }
    return temp[0] == target[0] && temp[1] == target[1] && temp[2] == target[2]
}

func max (i, j int) int { 
    if i > j {
        return i 
    } else { 
        return j 
    } 
}
```