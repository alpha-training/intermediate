# Rolling sum with reset

Given a time series of numbers, compute a rolling sum but reset to zero whenever a value is negative:

	q)rollingReset 2 3 -1 5 4 -2 7
	