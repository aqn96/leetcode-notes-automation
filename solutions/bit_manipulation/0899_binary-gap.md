# 899. Binary Gap

| Field | Value |
|---|---|
| **Difficulty** | Easy |
| **Pattern** | Bit Manipulation |
| **Time Complexity** | O(log n) |
| **Space Complexity** | O(1) |
| **Language** | python3 |
| **Solved On** | 2026-02-22 |
| **Tags** | Bit Manipulation |

## Key Insight

The solution efficiently tracks the positions of '1's in the binary representation of the number to calculate the maximum gap between them.

## Review Tip

> Understand how to manipulate bits and track indices when working with binary representations.

## Solution

```python
class Solution:
    def binaryGap(self, n: int) -> int:
        last_index = -1
        current_index = 0
        max_gap = 0

        while n > 0:
            if n & 1:
                if last_index != -1:
                    max_gap = max(max_gap, current_index - last_index)
                last_index = current_index
            
            n >>= 1 

            current_index += 1

        return max_gap
```
