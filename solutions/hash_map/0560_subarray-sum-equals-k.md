# 560. Subarray Sum Equals K

| Field | Value |
|---|---|
| **Difficulty** | Medium |
| **Pattern** | Hash Map |
| **Time Complexity** | O(n) |
| **Space Complexity** | O(n) |
| **Language** | python3 |
| **Solved On** | 2026-03-29 |
| **Tags** | Array, Hash Table, Prefix Sum |

## Problem

Given an array of integers `nums` and an integer `k`, return *the total number of subarrays whose sum equals to* `k`.

A subarray is a contiguous **non-empty** sequence of elements within an array.

 

Example 1:

```
Input: nums = [1,1,1], k = 2
Output: 2
```

Example 2:

```
Input: nums = [1,2,3], k = 3
Output: 2
```

 

**Constraints:**

	- `1 <= nums.length <= 2 * 104`
	- `-1000 <= nums[i] <= 1000`
	- `-107 <= k <= 107`

## Key Insight

The solution uses a hash map to store the frequency of prefix sums, allowing for efficient lookup of how many times a particular sum has occurred, which helps in determining the number of subarrays that sum to k.

## Review Tip

> Focus on understanding how prefix sums can simplify the problem of finding subarray sums.

## Solution

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        res = 0
        curSum = 0
        prefixsum = { 0 : 1 }

        for n in nums:
            curSum += n
            diff = curSum - k

            res += prefixsum.get(diff, 0)
            prefixsum[curSum] = 1 + prefixsum.get(curSum, 0)

        return res

```
