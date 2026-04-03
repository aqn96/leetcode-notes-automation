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

## Key Insight

The solution keeps track of the lowest buying price seen so far and calculates the potential profit at each price point, ensuring that we always consider the best possible profit from a valid buy-sell pair.

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
