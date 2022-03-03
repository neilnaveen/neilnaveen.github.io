---
title: "Leetcode-62"
date: 2022-03-03T11:28:06-05:00
toc: false
images:
tags : [leetcode,golang ]
---
[https://leetcode.com/problems/unique-paths/](https://leetcode.com/problems/unique-paths/)


### ***Explanation of solution***

Looking at this problem, we can recursively brute force all possible solutions by getting every single move we can make on the grid.
![](https://i.imgur.com/UeRxZ1M.jpg)

**First Solution: Brute Force Solution TLE**

``` go
func uniquePaths(m int, n int) int {
    if m == 1 || n == 1{ return 1 }
    return uniquePaths(m - 1, n) + uniquePaths(m, n - 1)
}
```

**Explanation of First Solution**
* Our base case is either when `m == 1` or `n == 1`
* If we have not hit our base case we add the recursive calls. We subtract from `m` making us move down and we subtract from `n` to move right.

---

Since the first solution, Time Limit Exceed, we can implement memoization to store results from a specific position in the matrix.


**Second Solution: Memoized Solution**
``` go
func uniquePaths(m int, n int) int {
    return pathFinder(m,n, map[[2]int]int{})
}
func pathFinder(m int, n int, dp map[[2]int]int) int {
    var pos = [2]int{ m, n }
    
    if (m == 1 || n == 1 ) || dp[pos] > 0 {
        return dp[pos] + 1 
    }
    
    dp[pos] = pathFinder(m - 1, n, dp) + pathFinder(m, n - 1, dp) - 1
    
    return dp[pos] + 1
}
```
---
**Explanation of Second Solution**

* The variable `pos` is the key for the `dp` map.
* The if statment ``(m == 1 || n == 1 ) || dp[pos] > 0`` is for when either we have hit the base case of `m` or `n` equaling `1` or `dp[pos] > 0` meaning that we have already stored the result of `dp[pos]`.
* `dp[pos] = pathFinder(m - 1, n, dp) + pathFinder(m, n - 1, dp) - 1` is when we add the recursive calls of pathFinder, one of the calls being when we subtract from `m` essentially moving down, and one of the calls being when we subtract from `n` making us move to the right


> ***Upvote if This Solution helps***