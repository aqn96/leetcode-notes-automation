# 14. Longest Common Prefix

| Field | Value |
|---|---|
| **Difficulty** | Easy |
| **Pattern** | String |
| **Time Complexity** | O(n * m) |
| **Space Complexity** | O(1) |
| **Language** | python3 |
| **Solved On** | 2026-03-14 |
| **Tags** | Array, String, Trie |

## Problem

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string `""`.

 

Example 1:

```
Input: strs = ["flower","flow","flight"]
Output: "fl"
```

Example 2:

```
Input: strs = ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.
```

 

**Constraints:**

	- `1 <= strs.length <= 200`
	- `0 <= strs[i].length <= 200`
	- `strs[i]` consists of only lowercase English letters if it is non-empty.

## Key Insight

The solution iteratively reduces the prefix by checking if it is a prefix of each string in the list until a common prefix is found or it becomes empty.

## Review Tip

> Always consider edge cases, such as empty input lists or strings with no common prefix.

## Solution

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if len(strs) == 0:
            return ""
        prefix = strs[0]
        for i in range(1, len(strs)):
            while strs[i].find(prefix) != 0:
                prefix = prefix[0 : len(prefix) - 1]
                if prefix == "":
                    return ""

        return prefix
```
