# Tickerlant logs

Here is the skeleton of a file `tpLogging.q`

	\p 5010
	
	\d .u

	T:`trade`quote
	N:100
	S:`JPM`GE`IBM
	C:T!(`time`sym`price;`time`sym`bid`ask)
	l:0Ni			/ handle to the log file, null to start with
	w:()!()

	initLog:{  
 	 ...
 	 }

	/ fill in your code from previous exercises
	upd:{[t;x]
	  ...
  	 }
  	
  	/ fill in your code from previous exercises
  	sub:{[t;s]
  	  ...
  	 }
  	
  	/ take from elsewhere
  	.z.pc:{...}
  	
  	/ saves us having to use a dummy feed
  	.z.ts:{
 		t:rand T;
 		n:1+rand N;
 		a:(n#.z.p;n?S),$[t=`trade;1#n;2#n]?\:100f;
 		upd[t;a];
 	 }

	initLog[];
	\t 1000

## File handles
Up to now, we'll have used `hopen` to open a connection to a process, but hopen also takes a file path as an argument
	
	hopen`:path.to.file.log

will return a handle, which can be stored in a variable.

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

NOTE: In these instructions, wherever we refer to variables  `i`, `l`, `L`, remember that these exist in `.u.`, but because you won't need the `.u` in your own code, we omit the `.u`.

The purpose of the task is to:

* Define a global variable `L` in the `.u` namespace which should be the path`:logs/tp[date]` using today's date e.g. `:logs/tp2025.06.01` 
* If the path `L` doesn't exist, initialise it with `L set ()`
* if `l` (the handle to the log file) is non null, use protected execution and `hclose` to close the handle
* Use `hopen` to open a handle to the log file and assign it to `l`
* set `i` to the number of records in the log file

### upd

* Increment `i` by 1
* Use `C` to turn the incoming data into a table
* Write the data to the log file using `l`

### sub

The function should return a 2 element list. The first element should be:

1. The values of the variables `i` and `L` as a two element list
2. The string `"snapshot"`

Why this string? In the real world, the TP would return a snapshot of all the tables, but we don't want to spend time on that now.

## client.q
Write another script that does the following:

	upd:upsert
	
	h:hopen 5010
	
	sub:{
	 a:h(`.u.sub;`;`);
	 ...
	 }
	
	sub`;

If your script is working correctly, it will:

* replay the relevant records in `L`
* Be subscribed to future updates from the tp