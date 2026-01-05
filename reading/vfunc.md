# Vector function

Let's take the following table:

	q)t:([]sym:10#`JPM`JPM`IBM`JPM`BP;size:sums 10#1 2 -1 -1 2)
	q)t
	sym size
	--------
	JPM 1
	JPM 3
	IBM 2
	JPM 1
	BP  3
	JPM 4
	JPM 6
	IBM 5
	JPM 4
	BP  6

Let's imagine I wanted to get all the records in t where the average size (for that sym) was less than 4, I would have to do something like:

	q)select avgSize:avg size by sym from t
	sym| avgSize
	---| --------
	BP | 4.5
	IBM| 3.5
	JPM| 3.166667

and then maybe get these syms

	q)exec sym from(select avgSize:avg size by sym from t)where avgSize<4
	`IBM`JPM

and then use these to filter t:
		
	q)select from t where sym in(exec sym from(select avgSize:avg size by sym from t)where avgSize<4)
	sym size
	--------
	JPM 1
	JPM 3
	IBM 2
	JPM 1
	JPM 4
	JPM 6
	IBM 5
	JPM 4

Obviously this is not the most beautiful.

## Task
Write a function `vfunc` (vector func) that behaves as follows:

	q)select from t where 4>vfunc[(avg;size);sym]
	sym size
	--------
	JPM 1
	JPM 3
	IBM 2
	JPM 1
	JPM 4
	JPM 6
	IBM 5
	JPM 4		/ BP is excluded

	q)select from t where 1<vfunc[(first;size);sym]
	sym size
	--------
	IBM 2
	BP  3
	IBM 5
	BP  6		/ JPM is excluded

## Constraint
Your solution cannot use any q keywords that begin with the letter f (e.g. `first`).