# 917. Boats to Save People

| Field | Value |
|---|---|
| **Difficulty** | Medium |
| **Pattern** | Two Pointers |
| **Time Complexity** | O(n log n) |
| **Space Complexity** | O(1) |
| **Language** | python3 |
| **Solved On** | 2026-04-06 |
| **Tags** | Array, Two Pointers, Greedy, Sorting |

## Problem

You are given an array `people` where `people[i]` is the weight of the `ith` person, and an **infinite number of boats** where each boat can carry a maximum weight of `limit`. Each boat carries at most two people at the same time, provided the sum of the weight of those people is at most `limit`.

Return *the minimum number of boats to carry every given person*.

 

Example 1:

```
Input: people = [1,2], limit = 3
Output: 1
Explanation: 1 boat (1, 2)
```

Example 2:

```
Input: people = [3,2,2,1], limit = 3
Output: 3
Explanation: 3 boats (1, 2), (2) and (3)
```

Example 3:

```
Input: people = [3,5,3,4], limit = 5
Output: 4
Explanation: 4 boats (3), (3), (4), (5)
```

 

**Constraints:**

	- `1 <= people.length <= 5 * 104`
	- `1 <= people[i] <= limit <= 3 * 104`

## Key Insight

The solution uses a two-pointer technique to pair the lightest and heaviest people, maximizing the number of people saved per boat while ensuring the weight limit is not exceeded.

## Review Tip

> Always consider sorting and using two pointers for problems involving pairing or grouping elements based on constraints.

## Solution

```python
class Solution:
    def numRescueBoats(self, people: List[int], limit: int) -> int:
        people.sort()
        light = 0
        heavy = len(people) - 1
        boat_count = 0

        while light <= heavy:
            if people[light] + people[heavy] <= limit:
                light += 1
            
            heavy -= 1

            boat_count += 1

        return boat_count
```
