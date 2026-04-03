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

## Key Insight

The solution iteratively reduces the prefix by checking if it is a prefix of each string in the list until a common prefix is found or it becomes empty.

## Review Tip

> Focus on edge cases, such as empty input or strings with no common prefix, during your solution explanation.

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
