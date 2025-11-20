# Chaining
If I want to create a compound function that is the result of chaining various functions, I can do it in a number of ways.

## Monad(s) and @

	q)countString:count string@
	q)countString `JPM
	3

Here we have two monads (we could have more) followed by `@`, which tells q that an argument has yet to come. If we just do:

	q)count string
	1

we are just running the `count` function with an argument of another (single) function, giving us 1.

We can chain as many monads as we like:

	q)negCountString:neg count string@
	q)negCountString `JPM
	-3

Chaining also works if the last function is a projection of a dyad, because a projection of a dyad is still a monad:

	q)isOdd:1=mod[;2]@
	q)isOdd til 5
	01010b
	
## Trailing each

	q)f:string reverse each
	q)d:`a`b!(10 20; 100 200 300)
	q)f d
	a| ("20";"10")
	b| ("300";"200";"100")

In this case, becase we have finished with an adverb, no trailing `@` is required.

## Using () and each

Here are two different ways of doing the same thing:

	q){upper string x}each`jpm`ge
	"JPM"
	"GE"
	q)(upper string@)each`jpm`ge
	"JPM"
	"GE"
	 
	