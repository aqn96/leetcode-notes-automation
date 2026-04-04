# 20. Valid Parentheses

| Field | Value |
|---|---|
| **Difficulty** | Easy |
| **Pattern** | Stack |
| **Time Complexity** | O(n) |
| **Space Complexity** | O(n) |
| **Language** | python3 |
| **Solved On** | 2026-03-14 |
| **Tags** | String, Stack |

## Problem

Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

	- Open brackets must be closed by the same type of brackets.
	- Open brackets must be closed in the correct order.
	- Every close bracket has a corresponding open bracket of the same type.

 

Example 1:

**Input:** s = "()"

**Output:** true

Example 2:

**Input:** s = "()[]{}"

**Output:** true

Example 3:

**Input:** s = "(]"

**Output:** false

Example 4:

**Input:** s = "([])"

**Output:** true

Example 5:

**Input:** s = "([)]"

**Output:** false

 

**Constraints:**

	- `1 <= s.length <= 104`
	- `s` consists of parentheses only `'()[]{}'`.

## Key Insight

The solution uses a stack to keep track of opening parentheses and ensures that each closing parenthesis matches the most recent opening one. This effectively validates the parentheses structure.

## Review Tip

> Always consider edge cases, such as empty strings or unmatched parentheses, when discussing your solution.

## Solution

```python
class Solution:
    def isValid(self, s: str) -> bool:
        # Initiate a stack
        stack = []
        
        mapping = {
        ")" : "(",
        "]" : "[",
        "}" : "{"
        }

        for char in s:
            if char in mapping.values():
                stack.append(char)
            elif char in mapping.keys():
                if not stack or stack.pop() != mapping[char]:
                    return False

        return not stack
```
