---
title: "Leetcode-1323"
date: 2021-07-03T18:37:13-05:00
toc: false
images:
tags : [leetcode,math,golang ]
---


[1323. Maximum 69 Number](https://leetcode.com/problems/maximum-69-number/)

This problem is about taking a number, let's say`9669`, and making it the largest number it could be by flipping one digit from a `6` to a `9`.

The solution is made up of five parts.
1. We split the number into its digits.

1. We reverse the array of digits because it reverses the order when you split a number as we did.

1. Then we find the first `6` and change it to a `9`.

1. Just before adding all the numbers together in a sum, we had to figure out how much should the digits be multiplied by to get into their correct form and not just the digits added up

3. Last of all, in code, the digits are summed up

***

In the first part, we add the last digit to the array and dividing the number by ten to take the last digit off. Now we have an array of the digits of the number reversed
``` c
for num > 0{
    arr = append(arr,num % 10)
    num/=10
}
```

***

This is the part for reversing the array. We have pointers on either side of the array. We then flip the digit on the left pointer digit with the right pointer digit and vice versa.

```c
l , r := 0 , len(arr)-1
for l < r{ 
    arr[l] , arr[r] = arr[r] , arr[l]
    l++
    r--
}
```

***

This part finds the first digit that is a `6` and changes it to a `9`  by finding the first occurrence of a `6`, changing it to a 9, and breaking out of the loop, so it doesn't change more than one digit
``` c
for i2,i := range arr{
    if i == 6{
        arr[i2] = 9
        break
    }
}
```

***

This part gets the number that we have to multiply to the last digit to get its place value. It does this by multiplying one by 10 for how long the number is `-1.`
```c
nummultiplyer := 1
for i := 0 ; i < len(arr)-1 ; i++{
    nummultiplyer *= 10
}
```

***

This is the last part where we add all the digits to a sum. If we add the digits all by itself, the sum will not be like the given number with one digit flipped. We need to add the numbers times the `nummultiplyer` so it is at the correct place value and then divide the `nummultiplyer` by ten to the correct place value for the next number.
```c
for _,i := range arr{
    sum += i*nummultiplyer
    nummultiplyer/=10
}
return sum
```
End Code
***

``` go
func maximum69Number(num int) int {
    sum := 0
    arr := []int{}
    
    // dividing up the number into a list of its digits
    for num > 0{
        arr = append(arr,num%10)
        num/=10
    }
    
    // reversing the list of digits , becasue when we made a list of all of the digits it was reversed
    l , r := 0 , len(arr)-1
    for l < r{ 
        arr[l] , arr[r] = arr[r] , arr[l]
        l++
        r--
    }
    
    // this is for finding the first digits that is a 6 and changing it to a nine
    for i2,i := range arr{
        if i == 6{
            arr[i2] = 9
            break
        }
    }
    // this is for finding how many zeros should the first number be multiplyed by
    nummultiplyer := 1
    for i := 0 ; i < len(arr)-1 ; i++{
        nummultiplyer *= 10
    }
    //and this is for remaking the list back into a array
    for _,i := range arr{
        sum += i*nummultiplyer
        nummultiplyer/=10
    }
    return sum
}
```
