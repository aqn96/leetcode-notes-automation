# 9. Palindrome Number

| Field | Value |
|---|---|
| **Difficulty** | Easy |
| **Pattern** | Two Pointers |
| **Time Complexity** | O(n) |
| **Space Complexity** | O(n) |
| **Language** | python3 |
| **Solved On** | 2026-03-29 |
| **Tags** | Math |

## Problem

Given an integer `x`, return `true`* if *`x`* is a ****palindrome****, and *`false`* otherwise*.

 

Example 1:

```
Input: x = 121
Output: true
Explanation: 121 reads as 121 from left to right and from right to left.
```

Example 2:

```
Input: x = -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

Example 3:

```
Input: x = 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```

 

**Constraints:**

	- `-231 <= x <= 231 - 1`

 

**Follow up:** Could you solve it without converting the integer to a string?

## Key Insight

The solution converts the integer to a string and uses two pointers to compare characters from both ends towards the center, checking for equality.

## Review Tip

> Always consider edge cases, such as negative numbers and single-digit integers, when solving palindrome problems.

## Solution

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        string = list(str(x))
        left = 0
        right = len(string) - 1

        while left < right:
            if string[left] != string[right]:
                return False
            left += 1
            right -= 1
        
        return True



```
