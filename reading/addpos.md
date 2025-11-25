# Add position

Unlike the other exercises, this is not an abstract puzzle. Instead, it brings us closer to the real world of trading.

## Useful snippet

	N:20
	trade:([]sym:N#`JPM`JPM`GE;side:N#`S`S`B`B`S`B;size:N#100 300 200 100 500 700 200)

## Task
The task is to write a function `addPos` whose job is to add a column called `pos` to the table argument.

In trading, your *position* is the running total of a given asset (e.g. stock shares) that you have bought or sold. For example, if I start the day by buying 100, my position is 100, and if I then sell 300, my position is now -200. When a position is positive I am *long*, and when it's negative I am *short*. The position is on a per sym basis. When a trader wants to go short, they need to borrow shares from somebody else with an understanding that they will repay them those shares at some time in the future. A trader will usually go long when they think the price is going to rise in the future, and they will go short when they think it will drop.

```
q)addPos trade
sym side size pos  
-------------------
JPM S    100  -100 
JPM S    300  -400 
GE  B    200  200  
JPM B    100  -300 
JPM S    500  -800 
GE  B    700  900  
JPM S    200  -1000
JPM S    100  -1100
GE  B    300  1200 
JPM B    200  -900 
JPM S    100  -1000
GE  B    500  1700 
JPM S    700  -1700
JPM S    200  -1900
GE  B    100  1800 
JPM B    300  -1600
JPM S    200  -1800
GE  B    100  1900 
JPM S    500  -2300
JPM S    700  -3000