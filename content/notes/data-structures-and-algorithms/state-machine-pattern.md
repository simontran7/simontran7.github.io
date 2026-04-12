# State Machine Dynamic Programming Pattern

Consider the LeetCode problem [309. Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/).

Imagine a hypothetical trader, and a state machine that encodes the trading rules of the problem:
- **hold** state: the trader is holding exactly one stock at the end of this day.
- **sold** state: the trader just sold a stock today.
- **idle** state: the trader isn't holding a stock at the end of this day (either resting, or cooling down after a sale).
- **buy** transition: move into the _hold_ state by purchasing a stock.
- **sell** transition: move into the _sold_ state by selling the stock you hold.
- **rest** transition: remain in the same state without trading.

[diagram](https://excalidraw.com/#json=uPbjzqujj_0c7dNGPGvKY,gWETmJBstLn0PXGlyG0xPQ)

At each state, we are concerned with the maximum profit the trader may achieve while in that state. We can represent this in code by creating three lists `hold`, `sell`, `idle`, where each element at index `i` represents the _maximum profit_ on day `i` when the trader is in that state, calculated as follows:
- `sold[i] = hold[i - 1] + prices[i]`. If the trader sells today, they must have been holding yesterday, and they gain today’s price.
- `hold[i] = max(hold[i - 1], idle[i - 1] - prices[i])`. If the trader is holding today, either they were already holding yesterday, or they bought today, which implies they were in the idle state yesterday and must now pay today's price.
- `idle[i] = max(idle[i - 1], sold[i - 1])`. If the trader is idling today, either they were idling yesterday, or sold yesterday and isn't allowed to buy again because of the cooldown restriction.

The final maximum profit is calculated by taking the maximum profit on the last day for each state (although including the maximum profit when the trader is holding isn't that useful, since it will always be dominated by the maximum profit when they sell or are idling).

```python
import math

class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)

        sold = [0] * n
        hold = [0] * n
        idle = [0] * n

        hold[0] = -prices[0] # buy on day 0
        sold[0] = -math.inf  # can't sell on day 0
        idle[0] = 0          # do nothing on day 0

        for i in range(1, n):
            sold[i] = hold[i - 1] + prices[i]
            hold[i] = max(hold[i - 1], idle[i - 1] - prices[i])
            idle[i] = max(idle[i - 1], sold[i - 1])

        return max(sold[n - 1], hold[n - 1], idle[n - 1])
```

