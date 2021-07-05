---
title: "Leetcode-1413"
date: 2021-07-05T10:52:20-05:00
images:
tags: [leetcode,array,golang, math]

[1413. Minimum Value to Get Positive Step by Step Sum](https://leetcode.com/problems/minimum-value-to-get-positive-step-by-step-sum/)

The idea of the v2 solution is to find the smallest number in the step-by-step sums if your start num is ```1```.  In the end, we add the smallest number we can to make all the steps positive

v2 solution O(n) time and O(1) space
```go 
func minStartValue(nums []int) int {
    sum := 1
    min := 100000000
    for _,j := range nums{
        sum += j
        if sum <= min{
            min = sum
        }
    }
    if min > 0 {
        return 1
    }
    return (min*-1)+2
}
```
v1 solution O(m*n) time and O(1) space

This is a brute force solution where we test from ```0``` to a ```10000000000``` for the start num in ```m``` time and see if the step by step addition is greater than ```0```

```go
func minStartValue(nums []int) int {
    sum := 0
    for i := 1 ; i < 10000000000 ; i++{
        sum = i
        flag := true
        for _,j := range nums{
            
            sum += j
            if sum <= 0{
                flag = false
                break
            }
        }
        if flag{
            return i
        }
       
    }
    return 0
}
```