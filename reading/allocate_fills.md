# Allocate fills

## Background
If we have 3 strategies that are buying `AAPL`, and the aggregated size gets filled for, say, 50, we want to allocate this 50 to the 3 strategies according to the priorities of their requests.

## Task
In `allocate.q`, the `allocate` function should allocate x proportionally to weighting y:

	q)allocate[100;.75 .2 .05]
	75 20 5
	q)allocate[100;.5 .5 .5]
	34 33 33