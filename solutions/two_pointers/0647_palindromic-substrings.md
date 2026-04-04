# 647. Palindromic Substrings

| Field | Value |
|---|---|
| **Difficulty** | Medium |
| **Pattern** | Two Pointers |
| **Time Complexity** | O(n^2) |
| **Space Complexity** | O(1) |
| **Language** | python3 |
| **Solved On** | 2026-03-29 |
| **Tags** | Two Pointers, String, Dynamic Programming |

## Problem

Given a string `s`, return *the number of **palindromic substrings** in it*.

A string is a **palindrome** when it reads the same backward as forward.

A **substring** is a contiguous sequence of characters within the string.

 

Example 1:

```
Input: s = "abc"
Output: 3
Explanation: Three palindromic strings: "a", "b", "c".
```

Example 2:

```
Input: s = "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
```

 

**Constraints:**

	- `1 <= s.length <= 1000`
	- `s` consists of lowercase English letters.

## Key Insight

The solution uses a two-pointer technique to expand around each character and each pair of characters to count palindromic substrings efficiently.

## Review Tip

> Focus on understanding how to identify and expand potential palindromic centers in a string.

## Solution

```python
class Solution:
    def countSubstrings(self, s: str) -> int:
        res = 0

        for i in range(len(s)):

            l = r = i
            res += self.countpali(l,r,s)

            l = i
            r = i + 1
            res += self.countpali(l,r,s)

        return res

    def countpali(self, l, r, s):
        res = 0
        while l >= 0 and r < len(s) and s[l] == s[r]:
            res += 1
            l -= 1
            r += 1
        return res
```
