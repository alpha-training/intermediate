## Ensuring something is a list

Let's suppose I am writing a function `negDistinct` I could do:

	q)negDistinct:{neg distinct x}
	q)negDistinct 3 3 4 5
	-3 -4 -5
	q)negDistinct 6
	'type
  		[1]  negDistinct:{neg distinct x}
                        ^
	q))\

This throws an error because distinct is an example of a function that requires a list for an argument; atoms won't work.

To get around this issue, I could think of using enlist, which *boxes* its argument into a list:

	q)negDistinct:{neg distinct $[1=count x;enlist x;x]}
	q)negDistinct 6
	,-6			/ looks good
	
But we have an edge case here that could cause us problems. What if we pass in a list of count 1 (aka a *singleton*)? Let's use `0N!` to print the results so we can see what's going on:

	q)0N!negDistinct enlist 6;
	,,-6

We can see that the expression `$[1=count x;enlist x;x]` holds true for both atoms & singletons, which means the enlist block gets run. This is not good, because we are now *double boxing* 6, creating a list in which the first item is itself a list of 1 (a singleton).

We could change our function to only run enlist on atoms, like so:

	q)negDistinct:{neg distinct $[(1=count x)&0>type x;enlist x;x]}
	q)q)0N!negDistinct enlist 6;
	,-6

but this is very messy. Is there another way?

## () is your friend

There is an easy solution. if we do either of

	x,()
	(),x

we will ensure that if x is an atom, we will get a singleton, and if x is a list, we will do nothing, since joining () to a list just gives us that list. So we can do any of:

	q)negDistinct:{neg distinct x,()}
	q)negDistinct:{neg distinct(),x}		/ saved a character!
	q)negDistinct:neg distinct(),			/ even nicer
	

In the last case, we don't need to include an `@` chaser because `,` is dyadic. See the [video](https://alphakdb-my.sharepoint.com/:v:/p/kieran_lucid/IQBWV4UQECOQTKbEZ6FP0deeAUF5flL8U37tLV-xIe8H0oc?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=fUYg2T) on simplifying functions and chaining if you haven't already done so.

## Summary

* When you want to create a list from an atom, you can use `enlist`
* When you want to ensure that an atom or list is a list, use `()` and `,` 
