# 121. Best Time to Buy and Sell Stock

| Field | Value |
|---|---|
| **Difficulty** | Easy |
| **Pattern** | Greedy |
| **Time Complexity** | O(n) |
| **Space Complexity** | O(1) |
| **Language** | python3 |
| **Solved On** | 2026-01-02 |
| **Tags** | Array, Dynamic Programming |

## Problem

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return *the maximum profit you can achieve from this transaction*. If you cannot achieve any profit, return `0`.

 

Example 1:

```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

Example 2:

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```

 

**Constraints:**

	- `1 <= prices.length <= 105`
	- `0 <= prices[i] <= 104`

## Key Insight

The core idea is to keep track of the lowest buying price encountered so far and calculate the potential profit at each price point, updating the maximum profit as needed.

## Review Tip

> Focus on understanding how to optimize decisions based on previous choices, as this is a common theme in greedy algorithms.

## Solution

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        # Start by assuming we buy on the first day
        buy_price = prices[0]

        # Initialize maximum profit to 0 (representing no trades)
        profit = 0

        # Iterate through prices starting from second day
        for p in prices[1:]:
            # If we find a lower price, update our buying price
            if buy_price > p:
                buy_price = p

            # Check if selling at current price gives better profit
            profit = max(profit, p - buy_price)

        return profit

```
