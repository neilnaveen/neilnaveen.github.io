---
title: "Leetcode-2154"
date: 2022-02-24T16:51:02-05:00
toc: false
images:
tags : [leetcode,golang ]
---

[2154](https://leetcode.com/problems/keep-multiplying-found-values-by-two/discuss/1734533/Iterative-One-Loop-O(1)-Space-solution)

**Intiution**

* We can iterate throught the whole array, if we find an element the same as original, we multiply original by 2 and reiterate through the  the array with the new original .
  ![image](https://assets.leetcode.com/users/images/c1d55ad0-f192-411c-9dde-3c8134432c2a_1643643211.7857583.gif)





``` go 
func findFinalValue(nums []int, original int) int {
    for i := 0 ; i < len(nums) ; i++{
        if nums[i] == original {
            original *= 2
            i = -1
        }
    }
    return original
}
```