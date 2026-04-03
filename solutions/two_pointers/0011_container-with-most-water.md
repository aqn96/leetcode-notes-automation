# 11. Container With Most Water

| Field | Value |
|---|---|
| **Difficulty** | Medium |
| **Pattern** | Two Pointers |
| **Time Complexity** | O(n) |
| **Space Complexity** | O(1) |
| **Language** | python3 |
| **Solved On** | 2026-03-15 |
| **Tags** | Array, Two Pointers, Greedy |

## Key Insight

The solution uses a two-pointer approach to efficiently find the maximum area by moving the pointers based on the heights of the lines, ensuring that we always consider the widest possible container first.

## Review Tip

> Focus on understanding how to optimize space and time complexity using pointers or other techniques in similar problems.

## Solution

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        max_area = 0
        left = 0
        right = len(height) - 1

        while left < right:
            max_area = max(max_area, (right - left) * min(height[left], height[right]))
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1
        
        return max_area
```
