# Window join

While `aj` gives us the state of the world at the moment of a trade, `wj` allows us a more detailed lookt at *what's going on* in and around the time of the trade.

## Task
The task is to flesh out `joinBest` and `joinAll` in `wj.q`:

	rfills:reverse fills reverse@

	Trade:([]time:"t"$09:30:00+sums 20#2 1 3 4 2 1;sym:`AAPL;price:10.08 10.01 10.07 10.02 10.04 10.05 10.04 10.02 10.07 10.08 10.05 10.06 10.04 10.01 10.03 10.03 10.07 10.08 10.02 10.01;size:900 200 1000 600 500 700 700 200 900 600 500 1000 300 800 100 200 1000 300 200 900)
	Quote:aj[`sym`time;([]time:09:30:00.0+sums 100*100#6 5 8 1 2;sym:`AAPL);Trade];
	update rfills price,rfills size from `Quote;
	update bid:price-.01*1+i mod 5,ask:price+.01*1+i mod 4 from `Quote;
	Trade,:update sym:`IBM,time+1000,price+5,reverse size from Trade
	Quote,:update sym:`IBM,time+1000,bid+5,ask+5 from Quote
	update `g#sym from`Quote;

	getTrades:{[s] select from Trade where sym in s}
	getQuotes:{[s] update `g#sym from select from Quote where sym in s}

	joinBest:{[s;win;b]
  	 t:getTrades s;
 	 q:getQuotes s;
	 /	...
 	 }

	joinAll:{[s;win;b]
  	  t:getTrades s;
  	  q:getQuotes s;
  	  / ...
 	 }
 
And test with various arguments:
 
	/ give me the best bid and ask from 100ms before each trade to 50ms after
	/ and use the prevailing quote before the window starts
	q)joinBest[`AAPL`IBM;-100 50;1b]	
	
	/ give me all the bids and asks 100ms either side of each trade, just for `IBM
	/ and only consider quotes within the window (do not use prevailing quote)
	q)joinAll[`IBM;-100 100;0b]