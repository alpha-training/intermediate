# Demystifying k

If we take the following two q keywords:

	q)avg 50 26 76 149			/ compute the average of a vector
	avg
	q)signum -3 5 7 -7 0		/ return the signs of each number
	-1 1 1 -1 0i
	
When we type these into the q terminal without an argument, we get different responses:

	q)avg
	avg
	q)signum
	k){(x>0)-x<0}

In the first case, we just get the keyword repeated back to us, and in the latter case we get what looks like q code, with `k)` at the start. Why is this? 

## The contents of QHOME
The answer lies in the `QHOME` folder where we are running q from. If it has been set, we can check it with:

	echo $QHOME		/ linux or mac
	echo %QHOME%		/ windows

This folder should contain the following files:

	kc.lic		/ a license file, not human readable
	q.k			/ a file of the k language, readable

And one of the following three folders, depending on your operating system:

	m64 	/ mac
	l64		/ linux
	w64		/ windows

Ynderneath this *64 folder folder, we'll have the q executable, which is not human readable.

Kx writes q in the c language, which is then compiled, to give us this executable.

`avg` is an example of a keyword that is *baked in* to this executable, which is why when we type `avg` into the q terminal, all we get is they keyword repeated back to us.

`signum` is not part of the core executable, it is contained in the file `q.k`, which is a k file, not a q file.

## What is k?
q/kdb+ was written by [Arthur Whitney](https://www.linkedin.com/in/arthurwhitney/). His company, Kx Systems, was formed by him and his wife Janet Lustgarten in 1993. The first version of the language was called k, which went through a couple of iterations. This is where kdb gets its name. K was hard to read - instead of `string` you had `$`, and `first` was `*`.

In the early 2000s, kdb was upgraded to the new kdb+. It shipped with a new version of k — k4 —, with a wrapper language called `q`, whose goal was to broaden the appeal of kdb+.

And this is what we have in q.k, it is definitions of various functions in the k4 language. From q, you can load q and k4 files natively. You can also put q code in a k file by adding `q)` at the start of the line, and you can add k code to a q file by putting `k)` at the start of the line.

## Aliases
Some functions like `signum` entail some logic, others are just straight translations 

	q)string
	$:
	q)first
	*:
	q)reverse
	|:

From a q session, we can move to and from the k language using a single `\`
	
	q)string`hey
	"hey"
	q)
	q)\				/ a single backslash moves us to k
	 $`hey			/ $ is string
	"hey"
	?3 4 5 5 8	/ ? is distinct

## What about the :

Many keywords such as `string`, `first`, `reverse` have a `:` in the definition. Let's focus on `string` (which is `$:` in k) 

	q)string
	$:
	q)\
  	 $:`hey
	"hey"
     $`hey
    "hey"

Since both work, why do we need the `:`? We need it because like most operators, `$` is heavily overloaded. For example (in q), `$` is used for both casting and for if-else. In k, there is even more overloading because is an additional *monadic* overload i.e. `$ arg` is an expression you can see in k (it means `string arg`), but never in q.

In k, when doing:

	NOUN1 VERB NOUN2

k will treat the VERB as the dyadic overload, which means behave as with q, but by doing

	NOUN1 VERB: NOUN2
	
We are forcing it to use the **monadic** overload of the verb. In other words, the `:` is a *disambiguator*, and is only needed when there is something to the left of the verb.

## Learning k
Why learn k? It is highly improbable that you will ever write your own code in k, as it would make it harder to read. But that does not mean that it isn't worth learning k. Here are some reasons:

* To truly understand how certain functions work (e.g. `aj`), it is good to know k
* The q.k was written by Arthur and the rest of the core kx team, making it some of the best code you'll ever read. It is therefore good to learn from the best
* Once you know the mappings (`string=$, distinct=?` etc), it gets easier quickly

## Mappings
Below are some q to k mappings. For simplicity, we will omit the `:`:

k        | q
|-------------|------
| count| #
| ~ | not
| @ | type
| ? | distinct
| ^ | null
| . | value / get
| & | where
| * | first
| - | neg
| + | flip
| _ | floor
| = | group
| \| | reverse


There are other k functions that can mean different things depending on the context:

k        | q1 | q2 | q3
|-------------|------|-----|----|
| ! | key (on a dict/keyed table) | til (on an atom) | inv (on a matrix)
| $ | string | mmu
| % | reciprocal | ltime
| < | iasc* (on a list) | hclose (on an atom)

\* includes short wrapper functions 

Some q functions are short combinations of k/q:

q        | k | q (in lambda form)
|-------------|------|-------|
| rand | {\*1?x}| {first 1?x}
| ceiling | -_-| {neg floor neg x}
| mod | {x-y*x div y} | same

Refer to the `q.k` file to read many more.

## A use case - disk segmentation
The .Q namespace is created in the q.k file. A good way to learn k (and good q!) is to take a common .Q function (e.g. `.Q.en`) and go expression by expression in k. For example:

	q).Q.par
	k){[d;p;t]`/:($[@!h:`/:d,`par.txt;`$":",h .q.mod[p;#h:0:h];d];`$$p;t)}    / yikes!

`.Q.par` is a useful function. It returns the path to a particular partition:

	q).Q.par[`:path/to/db;2025.01.01;`trade]
	`:path/to/db/2025.01.01/trade

A key feature is as follows. If a `par.txt` file exists in the directory, read it in, and use the row that corresponds to the expression

	p mod [number of rows in par.txt]

where p is the second argument (e.g. a date). So if par.txt has, say, 3 rows, corresponding to 3 disk locations, we can see that `.Q.par` would round robin across the 3 segments. Doing this instead of having all the date partitions in one folder, allows us to parallelise our processing across these segments, which in combination with slave threads, can speed up queries.

Just for testing we can do something like:

	$ q
	
	q)`:db/par.txt 0:("/dev/disk1";"/dev/disk2")
	q).Q.par[`:db;2025.01.01;`trade]
	`:/dev/disk1/2025.01.01/trade
	q).Q.par[`:db;2025.01.02;`trade]
	`:/dev/disk2/2025.01.02/trade
	q).Q.par[`:db;2025.01.03;`trade]
	`:/dev/disk1/2025.01.03/trade








