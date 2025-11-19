# The unique attribute (#u)

It is not immediately obvious how where `u# can be used. As a general rule, `u# becomes useful when you have **large** vectors of unique items. Here is an example.

We'll create a large sym vector using `genSyms`. The `sym` vector is the in memory equivalent of a sym file - basically what we'd find in memory when we load in a hdb dir:

	q)sym:genSyms 5

And let's create a list of 100 other syms:

	q)s100:100#genSyms 3
	q)s100
	`AAA`AAB`AAC`AAD`AAE`AAF`AAG`AAH`AAI`AAJ`AAK`AAL..

How long does it take to unique append (`?`) to the sym vector:

	q)\t `sym?s100
	21

That's quite long. Now let's create a separate sym vector with the unique attribute:

	q)usym:`u#sym
	q)\t `usym?s100
	0

Nice.

* Without `u#, the operator ? must look through the list
* With `u#, q can use hash lookup to more quickly determine which items on the left are new
	



