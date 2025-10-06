# Pyramid Path

In `solutions/pyramid.q`, write a function **pyr** to calculate the maximum path through a pyramid.

	q)P:1+(1+til 5)?'10	/ generate a random pyramid
	q)P
	,7
	4 9
	5 9 1
	2 1 6 2
	3 3 1 4 9
	q)
	q)pyr P
	35
	q)type pyr P		
	-7h					/ result is an atom, not a list
	
Needless to say, `pyr` should work for a pyramid of any size.

# What is a path?
	     7
	    4 9
	   5 9 1
	  2 1 6 2
	 3 3 1 4 9

For a number in a given row, a path connects it the adjacent numbers on the rows above and below. For example, the 6 on the fourth row connects to 9 and 1 above, and to 1 and 4 below. This is different for numbers at the end of a row e.g. 5 on the third row connects to 2 and 1 below and only 4 above.

# What is the maximum path?
The maximum path is the high number that can be reached by selecting a single (adjacent) number in each row. For example, 7 + 4 + 5 + 1 + 3 = 20 is legitimate path, but not the maximum path (35).

# Hints
* The adverb / (over) can help
* Starting from the bottom and working upwards can help




