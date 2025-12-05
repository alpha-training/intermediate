# Rolling sum with reset

Given a time series of numbers, compute a rolling sum but reset to zero whenever a value is negative:

	q)rollingSumWithReset 2 3 -1 5 4 0 -2 7 3
	2 5 0 5 9 9 0 7 10
	