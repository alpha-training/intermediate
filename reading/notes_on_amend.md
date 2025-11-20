# Notes on amend

Amend `@` (and its cousin `.`) is useful for multiple reasons.

## 1) Returning an amended list without changing the list
Let's say I have the following list:

	q)L:til 10
	q)L
	0 1 2 3 4 5 6 7 8 9

and for some reason I wanted to return the same list but with the 3rd and 4th items multiplied by ten, I can do:

	@[L;2 3;*;10]
	0 1 20 30 4 5 6 7 8 9

## 2) Avoiding repeat indexing
What if I wanted to change L permenantly?

In this case, there isn't much benefit to using @:

	q)L[2 3]*:10

is perfectly fine. But this brevity is only possible because of `*:`. What if for example we wanted to apply the square root to certain items in a list?

	q)F:9h$til 10		/ create a float list as sqrt will return a float
	q)F[2 3]:sqrt L 2 3
	
not very nice - we're  having to repeat the indices.

`@` can be our friend again:

	q)@[`F;2 3;sqrt]
	`F

much nicer. And what goes for `sqrt` goes for more complex functions.

## 3) Amending on disk

As we know from the kdb+ [data types](https://code.kx.com/q/basics/datatypes), types 1 to 19 have a predictable size e.g. int is 4 bytes, long is 8 bytes etc. This is unlike more complex types like table, dictionaries and strings (which are lists of characters).

It follows that if we create a vector L on disk like so:

	q)`:L set til 10
	`:L

kdb+ knows exactly where on disk each element in the list is stored. Because of this, we can set specific items in the vector without loading the file into memory:

	q)get`:L
	0 1 2 3 4 5 6 7 8 9
	q)@[`:L;3 8;:;1000]
	`:L
	q)get`:L
	0 1 2 1000 4 5 6 7 1000 9

**NOTE**: I can only **set** the values when using this on disk amend. Doing something like `2*` or `sqrt` is not possible because it would require reading in the values, in which case you are required to do the usual `get`, change and `set` of the full list.

Also, it is only pure vectors that can be amended in this fashion. Vectors cannot:

* be compressed
* be enumerated
* have an attribute
* be complex lists (e.g. a list of strings, or nested lists of numbers)

Even in the case of it having a sorted attribute (which is just a flag), it will fail:

	q)`:L set asc til 10	/ asc adds `s#
	`:L
	q)get`:L
	`s#0 1 2 3 4 5 6 7 8 9
	q)@[`:L;3 8;:;1000]
	'type/attr error amending file L
	  [0]  @[`:L;3 8;:;1000]
 	      ^

Please read this [On disk](https://code.kx.com/q/ref/amend/#on-disk) section for more info.

## 4) Debugging complex functions

Let's define two complex functions (that do more than one thing):

	q)A:100
	q)monad:{A*x xexp 5}
	q)dyad:{A*x xexp y}
	
Like we did with `sqrt`, we can now use `@` to apply these to our list:

	q)F:9h$til 10
	q)@[F;2 3;monad]
	0 1 3200 24300 4 5 6 7 8 9f
	@[F;2 3;dyad;5]
	0 1 3200 24300 4 5 6 7 8 9f
	@[F;2 3;dyad;4]
	0 1 1600 8100 4 5 6 7 8 9f

And we can also debug:

	q)dyad:{dbg;A*x xexp y}
	q)@[F;2 3;dyad;4]
	'dbg
  [1]  dyad:{dbg;A*x xexp y}
             ^
	q))x
	2f
	q))y
	4

If for example I wanted to call **dyad** with the 3 argument overload of `@`, I could use a projection:

	q)@[F;2 3;dyad[;4]]
	0 1 1600 8100 4 5 6 7 8 9f

I have projected **the second** argument to be consistent with how the 4 argument overload behaves with dyadic functions i.e. x comes from the list, y comes from me.

## . amend
The close cousin of `@` is `.`, which is used for amending **at depth**. For example, it can be used in the `.u.w` dictionaery in the tickerplant, or any list of depth. The difference between a dictionary and a nested list is that in the former case, the first index references the key(s) of the dictionary, not a numeric index.


