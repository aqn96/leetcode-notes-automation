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

## Problem

Given an array `nums` of size `n`, return *the majority element*.

The majority element is the element that appears more than `⌊n / 2⌋` times. You may assume that the majority element always exists in the array.

 

Example 1:

```
Input: nums = [3,2,3]
Output: 3
```

Example 2:

```
Input: nums = [2,2,1,1,1,2,2]
Output: 2
```

 

**Constraints:**

	- `n == nums.length`
	- `1 <= n <= 5 * 104`
	- `-109 <= nums[i] <= 109`
	- The input is generated such that a majority element will exist in the array.

 

**Follow-up:** Could you solve the problem in linear time and in `O(1)` space?

## Key Insight

The majority element in an array appears more than n/2 times, so sorting the array allows us to find it at the middle index after sorting. This is a straightforward approach but not the most efficient.

## Review Tip

> Consider discussing alternative solutions, such as using a hash map or the Boyer-Moore Voting Algorithm for better efficiency.

## Solution

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        nums.sort()

        return nums[len(nums) // 2]
```
