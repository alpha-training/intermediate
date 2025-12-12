# From note

`fromnote` is the opposite of `tonote`. It returns a dictionary of keys to values, with the keys as symbol type:

	q)k:`strat1`strat2`strat3
	q)v:100 200 300
	q)
	q)tonote[k;v]
	"strat1=100 strat2=200 strat3=300"
	q)
	q)fromnote tonote[k;v]
	strat1| "100"
	strat2| "200"
	strat3| "300"

The values remain as strings, which is the desired behaviour; users can cast the results afterwards if they wish.

**Hint 1:** There are multiple ways to do this, including `vs` and `0:`. You should experiment with all.

**Hint 2**: An expression that may or may not be useful, depending on your solution, is `(FUNCTION). ARGS`.  Here are some unrelated examples of functions that use this:
 
	q)(mod). 11 3
	2
	q)(+). 3 4
	7

It is essentially shorthand for `func[args 0;args1]` or `args[0] operator args 1`;