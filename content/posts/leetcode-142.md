---
title: "Leetcode-142"
date: 2022-01-19T11:28:06-05:00
toc: false
images:
tags : [leetcode,math,golang ]
---

[https://leetcode.com/problems/linked-list-cycle-ii/](https://leetcode.com/problems/linked-list-cycle-ii/)
### **Intuiton for both solutions**
* If we keep on going through the linked list, there are two outcomes, Either we keep on iterating forever, or there is no cycle, and the loop ends
* We could store all are previously visited nodes to see if we have already visited them on a map
* If we find a node that is already in the map, we return that node
* If we break out of the linked list, we return nil because there is no cycle


**How the Two Pointer Solution Works**

In a nutshell, the two-pointer solution is just a sped-up version of the brute force solution. All we do is have a fast and slow pointer. We map the points we have visited with the slow pointer, and the fast pointer jumps ahead and checks if we have already seen that point.

Brute Force O(n) time
``` go 
func detectCycle(head *ListNode) *ListNode {
    m := make(map[*ListNode]bool)
    curr := head
    for curr != nil{
        if m[curr] == true{
            return curr
        }
        m[curr] = true
        curr = curr.Next
    }
    return nil
}
```

Two Pointer Solution O(n/2) time
``` go
func detectCycle(head *ListNode) *ListNode {
    slow,fast := head,head
    visited := map[*ListNode]bool{}
    for fast != nil{
        
        visited[slow] = true
        slow = slow.Next
        fast = fast.Next
        if visited[fast] {
            return fast
        }
        if fast != nil{
            fast = fast.Next
        }
        if visited[fast] {
            return fast
        }
        
    }
    return nil
}
```


