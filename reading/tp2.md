# Tickerplant V0.2
Now we are going to enhance our tickerplant further by introducing binary logging to disk. The 'binary' in this context denotes the fact that we are not logging readable text, but kdb+'s proprietary format, which is not human readable.

## A word about binary and hex
It is a stream of hex e.g

	q)`:L set 10 20 30h
	q)read1`:L
	0xfe2005000000000000000000000000000a0014001e00   / vector of hex
	q)"j"$read1`:L
	254 32 5 0 0 0 0 0 0 0 0 0 0 0 0 0 10 0 20 0 30 0
	
So we can see that q stores this vector has numbers, with some basic header information, part of which is the sorted attribute flag:

	q)"j"$read1`:L
	254 32 5 0 0 0 0 0 0 0 0 0 0 0 0 0 10 0 20 0 30 0
	q)"j"$read1`:sL set`s#10 20 30h
	254 32 5 1 0 0 0 0 0 0 0 0 0 0 0 0 10 0 20 0 30 0    / notice the 4th number
	
Hex is base 16, which makes it hard to read. Humans are used to dealing with base 10 i.e. once we get to the number 9, we 'cycle' upwards.

In Hex, this 'cycling' doesn't happen until the number 16:

	q){x!"x"$x}til 20
	0 | 00
	1 | 01
	2 | 02
	3 | 03
	4 | 04
	5 | 05
	6 | 06
	7 | 07
	8 | 08
	9 | 09
	10| 0a			/ hex uses a-f to extend 0-9
	11| 0b	
	12| 0c
	13| 0d
	14| 0e
	15| 0f
	16| 10			/ doesn't cycle until here
	17| 11
	18| 12
	19| 13

So while a q binary might look intimidating, it shouldn't be - it's just numbers with a different base, and we can cast these numbers to long to make them easier to read.

## What is a tickerplant log file
A log file is a record of the updates that arrived in a tickerplant. Rather than stored as one blob like we do with `set`, a log file is written to by using `hopen` to open a handle to a file — yes you can do that — and pushing the data down the handle like you would with a handle to a process. **NOTE**: you do **not** negate handles to files. Unlike a text log file, a tickerlant log file is in q's proprietary (binary) format i.e. in hex.

## Global variables

Global variables can be defined from a function like so:

	q){a::10}[]
	q)a
	10

If I am in a namespace, the global variable will be defined in that namespace:

	q)\d .u
	q.u){i::10}[]
	q.u)i
	10
	q.u){i+:1}[]	/ amending happens globally
	q.u)i
	11
	q.u)\d .
	q).u
 	 | ::
	i| 11
	q)i	
	'i				/ i doesn't exist in root
  	[0]  i
       ^

## Task
Copy your code from **tp1.q** into **tp2.q**. When the word 'globally' is used here, we are talking about the .u namespace.

	/ tp2.q
	
	\l schema.q
	
	\d .u
	
	l:0Ni			/ handle to the log file, default to null
	i:0				/ number of updates received today, default to 0
	
	ld:{[d]	
		/ Accepts a date as its argument
		/ Defines L as `:logs/tp2025.06.01 (or whatever the date provided)
		/ if l (the handle to the log file) is non null, use protected execution and hclose to close the handle
		/ Use hopen to open a handle to the log file and assign it globally to l
		/ set i globally to the number of rows in the log file L
	 }
	 
	upd:{[t;x]
	 / Increment i by 1
	 / Place all the logic currently in tp1.q next
	 / Write the data to the log file in the format (`upd;t;x)
	 }
	 
 	tick:{
 		/ Define t globally (done outside function in tp1)
 		/ Define w	(same)
 		/ define d globally (as current date)
 		ld d;		/ call ld
 	 }
 	 
 	 \d .
	 
	.u.tick[];
	 

**Note:** When writing to a log file, be sure to enlist what you write, otherwise instead of:

	(`upd;`trade;[data])
	(`upd;`quote;[data])

you will get

	`upd
	`trade
	[data]
	`upd
	`quote
	[data]
	
which is not what we want!

At any time, you can do `get .u.L` to see what the contents of the log file are. Of course, this should not be done when the log file is large.

## client.q
Write another script that does the following:

	upd:insert
	
	h:hopen 5010
	
	sub:{
	 a:h"(.u.sub[`;`];`.u `i`L)";
	 / Replay the log using the second item in a
	 }
	
	sub`;

If your script is working correctly, it will:

* replay the relevant records in `L`
* Be subscribed to future updates from the tp