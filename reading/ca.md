# Corporate actions

Corporate actions are events that have a bearing on the price of a stock. Examples include:

* **Dividends**: Prices tend to rise towards a dividend date, and suddenly fall thereafter
* **Stock splits and reverse splits**: A firm may decide to double the number of stocks in issue, or vice versa if the price is too low
* **Bonus**: Shareholders are given extra shares for free, which is similar to a split

## Task
A trader querying historical day will often want to see prices that are adjusted to reflect the true price, regardless of these events. Here is some boiler plate code for your file `ca.q`:

	/ ca.q
	
	Trade:([]date:1995.01.01 2000.01.02 2000.02.02 2000.03.02 2000.04.02 2000.05.01;sym:`IBM;price:100f;size:100)
	Trade,:update sym:`JPM from Trade

	CA:([]date:2000.01.01 2000.02.01 2000.03.01 2000.04.01;sym:`IBM;typ:`split`dividend`bonus`dividend;factor:.5 .98 .8 .97)
	CA,:update sym:`JPM,factor-.01 from CA

	adjust:{[x;types]
  	  s:distinct x`sym;
  	  ca:select from CA where sym in s;
  	  if[not types~`all;ca:select from ca where typ in types];
      ...
  	 }

The task is to complete the `adjust` function such that:

	q)adjust[Trade;`dividend]
	date       sym price size     typ      factor rfactor
	-----------------------------------------------------
	1995.01.01 IBM 95.06 105.1967 dividend 1      0.9506 
	2000.01.02 IBM 95.06 105.1967 dividend 1      0.9506 
	2000.02.02 IBM 97    103.0928 dividend 0.98   0.97   
	2000.03.02 IBM 97    103.0928 dividend 0.98   0.97   
	2000.04.02 IBM 100   100      dividend 0.97   1      
	2000.05.01 IBM 100   100      dividend 0.97   1      
	1995.01.01 JPM 93.12 107.3883 dividend 1      0.9312 
	2000.01.02 JPM 93.12 107.3883 dividend 1      0.9312 
	2000.02.02 JPM 96    104.1667 dividend 0.97   0.96   
	2000.03.02 JPM 96    104.1667 dividend 0.97   0.96   
	2000.04.02 JPM 100   100      dividend 0.96   1      
	2000.05.01 JPM 100   100      dividend 0.96   1     
	q)
	q) 
	q)adjust[select from Trade where sym=`JPM;`all]
	date       sym price    size     typ      factor rfactor  
	----------------------------------------------------------
	1995.01.01 JPM 36.04675 277.4175 split    1      0.3604675
	2000.01.02 JPM 73.5648  135.9346 split    0.49   0.735648 
	2000.02.02 JPM 75.84    131.8565 dividend 0.97   0.7584   
	2000.03.02 JPM 96       104.1667 bonus    0.79   0.96     
	2000.04.02 JPM 100      100      dividend 0.96   1        
	2000.05.01 JPM 100      100      dividend 0.96   1       

**Note:** The adjustments are done in reverse. The latest prices are taken to be correct (1), and we cumulatively adjust the prices and sizes in reverse as we move back through time, hence the `rfactor` column is what is used. Why adjust size? In the case of a split, if the price is halved, the size needs to be doubled if we are to have historically meaningful sizes as well as prices.

## Testing
The word **notional** is often used to denote the product of size and price. In our stylized example of 100 size and 100 price, we know that the notional is 10,000 for every trade, before corporate actions are applied. We can check that this is the case in our result:

	q)update notional:price*size from adjust[Trade;`all]
	date       sym price    size     typ      factor rfactor   notional
	-------------------------------------------------------------------
	1995.01.01 IBM 38.024   262.9918 split    1      0.38024   10000   
	2000.01.02 IBM 76.048   131.4959 split    0.5    0.76048   10000   
	2000.02.02 IBM 77.6     128.866  dividend 0.98   0.776     10000   
	2000.03.02 IBM 97       103.0928 bonus    0.8    0.97      10000   
	2000.04.02 IBM 100      100      dividend 0.97   1         10000   
	2000.05.01 IBM 100      100      dividend 0.97   1         10000   
	1995.01.01 JPM 36.04675 277.4175 split    1      0.3604675 10000   
	2000.01.02 JPM 73.5648  135.9346 split    0.49   0.735648  10000   
	2000.02.02 JPM 75.84    131.8565 dividend 0.97   0.7584    10000   
	2000.03.02 JPM 96       104.1667 bonus    0.79   0.96      10000   
	2000.04.02 JPM 100      100      dividend 0.96   1         10000   
	2000.05.01 JPM 100      100      dividend 0.96   1         10000   
	