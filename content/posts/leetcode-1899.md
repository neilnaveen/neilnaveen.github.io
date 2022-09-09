---
title: "Leetcode-1899"
date: 2022-09-03T11:28:06-05:00
toc: false
images:
tags : [leetcode,golang]
---
In this solution, we are looking at all the triplets with values smaller than or equal to those in the target array and merging them into one triplet, `temp`.

This way, we will make the greatest triplet that is smaller than equal to the target triplet, which will be the closest we can make to target.

``` go
func mergeTriplets(triplets [][]int, target []int) bool {
    var temp [3]int
    // Loop through all triplets
    for _, i := range triplets {
        // If the triplet is smaller than or equal to the target
        if i[0] <= target[0] && i[1] <= target[1] && i[2] <= target[2] {
            // If the triplet is smaller than or equal to the target, merge it into temp
            temp = [3]int{max(i[0],temp[0]), max(i[1],temp[1]), max(i[2], temp[2])}
        } 
    }
    // If temp is equal to target, return true
    return temp[0] == target[0] && temp[1] == target[1] && temp[2] == target[2]
}
// Helper function to get the max of two numbers
func max (i, j int) int { 
    if i > j {
        return i 
    } else { 
        return j 
    } 
}
````