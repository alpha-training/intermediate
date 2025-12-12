# To note (tonote.q)

A note column can be useful for post trade analysis. The function `tonote` should accept two vectors for arguments, and return `"x0=y0 x1=y1..."`

## Example: Intent to Agg

`Intent` tells us the target position per sym/strategy:

	q)Intent:([]sym:10#`JPM`JPM`GE;strat:`$"strat",/:string 1+til 10;tgtpos:sums 10#500 -200 300)
	q)Intent
	sym strat   tgtpos
	------------------
	JPM strat1  500   
	JPM strat2  300   
	GE  strat3  600   
	JPM strat4  1100  
	JPM strat5  900   
	GE  strat6  1200  
	JPM strat7  1700  
	JPM strat8  1500  
	GE  strat9  1800  
	JPM strat10 2300


If we want to derive an `Agg` table (aggregations) across strats, a note column shows which strats constitute each quantity:

	q)select aggtgt:sum tgtpos,note:tonote[strat;tgtpos]by sym from Intent
	sym| aggtgt note                                                                               
	---| ------------------------------------------------------------------------------------------
	GE | 3600   "strat3=600 strat6=1200 strat9=1800"                                               
	JPM| 8300   "strat1=500 strat2=300 strat4=1100 strat5=900 strat7=1700 strat8=1500 strat10=2300"

**Note** Because we cannot know whether one or both of the arguments to `tonote` will be a list of strings, in your function you can use `tostr`, defined at the top of your file:

	tostr:{$[0=count x;"";0=t:type x;.z.s each x;t in -10 10h;x;string x]}
	