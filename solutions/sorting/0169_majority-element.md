# 169. Majority Element

| Field | Value |
|---|---|
| **Difficulty** | Easy |
| **Pattern** | Sorting |
| **Time Complexity** | O(n log n) |
| **Space Complexity** | O(1) |
| **Language** | python3 |
| **Solved On** | 2025-12-20 |
| **Tags** | Array, Hash Table, Divide and Conquer, Sorting, Counting |

## Key Insight

The majority element in an array is guaranteed to be at the middle index after sorting, as it appears more than half the time.

## Review Tip

> Always consider the most efficient approach and edge cases when solving problems.

## Solution

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        nums.sort()

        return nums[len(nums) // 2]
```
