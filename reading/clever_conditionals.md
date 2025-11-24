# Clever conditionals

You'll often find yourself wanting to return different values depending on a conditional e.g.

	q)oddEven:{$[x mod 2;`odd;`even]}
	q)oddEven 5
	`odd
	q)oddEven 6
	`even

the above doesn't work for lists. The **vector conditional** expression can be used instead:

	?[list_of_booleans;if-true;if-false]

e.g.
	
	q)?[0110b;`yes;`no]
	`no`yes`yes`no

So we can rewrite oddEven to use this:

	q)oddEven:{?[0=x mod 2;`even;`odd]}
	q)q)oddEven 5 6
	`odd`even
	q)oddEven 5
	`odd

## Indexing
Instead odf using `$` or `?`, we can think about using lists and indexing to handle conditionals e.g.

	q)oddEven:{`even`odd x mod 2}
	q)oddEven 5 6
	`odd`even

Notice what is happening here:
	
1. `0=x mod 2` returns 0 or 1
2. Which is used to index into the list

This is nice, and more *'q like'*. And for those of use who don't like typing, we can go further:

	q)oddEven:{`even`odd x mod 2}		/ current definition
	q)oddEven:`even`odd mod[;2]@		/ 1 character saved, woo hoo
	q)oddEven 5 8 9
	`odd`even`odd

## Dictionaries

There may be some cases where we want to use dictionaries instead of indexing:

	q)exchanges:`N`L`T!("New York / NYSE";"London / LSE";"Tokyo / TYO")
	q)trade:([]sym:10?`JPM`GE;size:10?100;ex:10?`N`L`T)
	q)update exInfo:exchanges ex from`trade

## Conclusion
These are just some tricks you should bear in mind when handling conditionals.