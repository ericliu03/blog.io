---
title: "[Leetcode 714] Best Time to Buy and Sell Stock with Transaction Fee"
description: ""
date: "2019-05-07T10:53:59-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "dp"
---



## Question

[Leetcode 714: Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

Your are given an array of integers `prices`, for which the `i`-th element is the price of a given stock on day `i`; and a non-negative integer fee representing a transaction `fee`.

You may complete as many transactions as you like, but you need to pay the transaction fee for each transaction. You may not buy more than 1 share of a stock at a time (ie. you must sell the stock share before you buy again.)

Return the maximum profit you can make.

### Note:
1. `0 < prices.length <= 50000`.
1. `0 < prices[i] < 50000`.
1. `0 <= fee < 50000`.


### Examples

Example 1:
```
Input: prices = [1, 3, 2, 8, 4, 9], fee = 2
Output: 8
Explanation: The maximum profit can be achieved by:
Buying at prices[0] = 1
Selling at prices[3] = 8
Buying at prices[4] = 4
Selling at prices[5] = 9
The total profit is ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```


## Solution
### Method 1

On each day, we hold two values represent our profit in two states: if we have cash, or if we hold stock.

1. `cash[i] = max(cash[i-1], hold[i-1] + price[i] - fee)`
1. `hold[i] = max(hold[i-1], cash[i-1] - price[i])`

#### Code
```python
class Solution:
    def maxProfit(self, prices: List[int], fee: int) -> int:
        cash, hold = 0, -prices[0]
        
        for i in range(1, len(prices)):
            cash = max(cash, hold + prices[i] - fee)
            hold = max(hold, cash - prices[i])
        
        return cash
```