# 72. Edit Distance

| Field | Value |
|---|---|
| **Difficulty** | Medium |
| **Pattern** | Dynamic Programming |
| **Time Complexity** | O(m * n) |
| **Space Complexity** | O(m * n) |
| **Language** | python3 |
| **Solved On** | 2026-04-09 |
| **Tags** | String, Dynamic Programming |

## Problem

Given two strings `word1` and `word2`, return *the minimum number of operations required to convert `word1` to `word2`*.

You have the following three operations permitted on a word:

	- Insert a character
	- Delete a character
	- Replace a character

 

Example 1:

```
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
```

Example 2:

```
Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```

 

**Constraints:**

	- `0 <= word1.length, word2.length <= 500`
	- `word1` and `word2` consist of lowercase English letters.

## Key Insight

The problem can be solved using a 2D DP table where each cell represents the minimum edit distance between substrings of the two words. The solution builds upon previously computed values to find the optimal edit distance.

## Review Tip

> Always consider edge cases and ensure your base cases are correctly initialized in dynamic programming problems.

## Solution

```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        cache = [ [float("inf")] * (len(word2) + 1) for i in range(len(word1) + 1)]
        for j in range(len(word2) + 1):
            cache[len(word1)][j] = len(word2) - j
        for i in range(len(word1) + 1):
            cache[i][len(word2)] = len(word1) - i

        for i in range(len(word1) - 1, -1, -1):
            for j in range(len(word2) - 1, -1, -1):
                if word1[i] == word2[j]:
                    cache[i][j] = cache[i + 1][j + 1]
                else:
                    cache[i][j] = 1 + min(cache[i + 1][j], cache[i][j + 1], cache[i+1][j+1])
        
        return cache[0][0]

```
