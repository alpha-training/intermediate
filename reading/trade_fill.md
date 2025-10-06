# Trade fill

In `solutions/tradeFuncs.q` write a function with the following signature:

	tradeFill:{[size;orders] .... }

the purpose of tradeFill is to return the *filled* sizes of an incoming order against the orders already sitting on the order book.

## Explanation
On an exchange, let's imagine that we have an order book that looks like so:

| Buy Sizes        | Price | Sell Sizes                    |
|-------------|------|----------|
| | 47.23| 60 355
| | 47.22| 20 7 8 12
| | 47.21| 10 7 9 11
| | 47.20| 
| 30 14 29 6 | 47.19| 
| 52 47 3| 47.18| 
| 200 300| 47.17| 

At each price level, we have a list of counterparties willing to buy / sell specific sizes, based on a First In First Out (FIFO) approach (from left to right).

Let's imagine a buy order arrives for 5 at 47.18. In such a scenario, we would just add a 5 after  `52 47 3`, and that buyer would be at the back of the queue at that price level.

Now let's imagine a very aggressive buy order for 20 comes in at 47.22. Such an order is said to *'cross the book'*, which is to say that it is at or above the lowest sell price (47.21). An exchange is obliged to offer buyers and sellers the best price possible, and in this scenario, the exchange would be forced to fill this prospective buyer at 47.21, as this is a lower (and better) price than 47.22. At this price level, there are four orders `10 7 9 11`.  To fill the order at this level, we would take 17 from the first two sizes, and 3 from the 9, in other words:
	
	q)tradeFill[20;10 7 9 11]
	10 7 3 0
	
Note that this order for 20 never *posts* to the book, which is to say that it never gets added to the book's list of orders; it merely takes liquidity by executing against the orders that are already on the book. In that sense our first example order at 47.18 was a liquidty *maker*, while the second was a liquidity *taker*. These are phrases that we will hear again.

Regarding the signature of `tradeFill`, feel free to use explicit arguments or `x y` - whatever arguments you like.