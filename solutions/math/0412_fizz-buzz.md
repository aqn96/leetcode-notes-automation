# 412. Fizz Buzz

| Field | Value |
|---|---|
| **Difficulty** | Easy |
| **Pattern** | Math |
| **Time Complexity** | O(n) |
| **Space Complexity** | O(n) |
| **Language** | python3 |
| **Solved On** | 2026-03-28 |
| **Tags** | Math, String, Simulation |

## Problem

Given an integer `n`, return *a string array *`answer`* (**1-indexed**) where*:

	- `answer[i] == "FizzBuzz"` if `i` is divisible by `3` and `5`.
	- `answer[i] == "Fizz"` if `i` is divisible by `3`.
	- `answer[i] == "Buzz"` if `i` is divisible by `5`.
	- `answer[i] == i` (as a string) if none of the above conditions are true.

 

Example 1:

```
Input: n = 3
Output: ["1","2","Fizz"]
```

Example 2:

```
Input: n = 5
Output: ["1","2","Fizz","4","Buzz"]
```

Example 3:

```
Input: n = 15
Output: ["1","2","Fizz","4","Buzz","Fizz","7","8","Fizz","Buzz","11","Fizz","13","14","FizzBuzz"]
```

 

**Constraints:**

	- `1 <= n <= 104`

## Key Insight

The solution iterates through numbers from 1 to n and uses modulo operations to determine the appropriate output for each number based on FizzBuzz rules.

## Review Tip

> Focus on understanding the problem requirements and edge cases before coding.

## Solution

```python
class Solution:
    def fizzBuzz(self, n: int) -> List[str]:
        res = []

        for i in range(1, n + 1):
            if i % 5 == 0 and i % 3 == 0:
                res.append("FizzBuzz")
            elif i % 5 == 0:
                res.append("Buzz")
            elif i % 3 == 0:
                res.append("Fizz")
            else:
                res.append(str(i))

        return res
```
