# 55. Jump Game

| Field | Value |
|---|---|
| **Difficulty** | Medium |
| **Pattern** | Greedy |
| **Time Complexity** | O(n) |
| **Space Complexity** | O(1) |
| **Language** | python3 |
| **Solved On** | 2026-04-10 |
| **Tags** | Array, Dynamic Programming, Greedy |

## Problem

You are given an integer array `nums`. You are initially positioned at the array's **first index**, and each element in the array represents your maximum jump length at that position.

Return `true`* if you can reach the last index, or *`false`* otherwise*.

 

Example 1:

```
Input: nums = [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

Example 2:

```
Input: nums = [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum jump length is 0, which makes it impossible to reach the last index.
```

 

**Constraints:**

	- `1 <= nums.length <= 104`
	- `0 <= nums[i] <= 105`

## Key Insight

The algorithm works by iterating backwards through the array and updating the furthest reachable index. If we can reach the start of the array (index 0), then it's possible to jump to the last index.

## Review Tip

> Focus on understanding the greedy approach and how to determine the furthest reachable index from each position.

## Solution

```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        distance = len(nums) - 1

        for i in range(distance, -1, -1):
            if i + nums[i] >= distance:
                distance = i

        return True if distance == 0 else False

        
```
