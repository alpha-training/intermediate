# Tickerlant logs

Here is the skeleton of a file `tpLogging.q`

	\p 5010
	
	\d .u

	T:`trade`quote
	N:100
	S:`JPM`GE`IBM
	C:T!(`time`sym`price;`time`sym`bid`ask)
	l:0Ni
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

This file is a bare bones tickerplant. Your job is to:

### initLog

* Define a variable global L variable in the .u namespace which should be `:logs/tp2025.01.01` (instead of using this date, use `string .z.d` to get the current date)
* If `(.u.)L` doesn't exists, initialise it with `L set ()` (no need for the .u prefix as you're alrteady in the namespace)
* if `(.u.)l` is non null, use protected execution and `hclose` to close the handle
* Use `hopen` to open a handle to the log file and assign it to `(.u.)l`
* set `(.u.)i` to the number of records in the log file

### upd

* Increment `(.u.)i` by 1
* Use `.u.(C)` to turn the incoming data into a table
* Write the data to the log file using `.u.(l)`

### sub

The function should return a 2 element list. The first element should be:

1. .u.i and .u.L as a two element list
2. The string `"snapshot"`

Why this string? Because in the real world, the TP would return a snapshot of all the tables, but we don't want to spend time on that now.

## client.q
Write another script that does the following:

	upd:upsert
	
	h:hopen 5010
	
	sub:{
	 a:h(`.u.sub;`;`);
	 ...
	 }
	
	sub`;

If your script is working correcly, it will:

* replay the relevant records in `.u.L`
* Be subscribed to future updates from the tp