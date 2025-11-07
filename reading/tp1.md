# Tickerplant Changes 1
The tickerplant we used in the fundamentals course was a minimalistic implementation, what we want to do now is to make some changes to professionalise it. The first of these changes will be to add per sym subscriptions.

## .u.w structure
The structure of the subscription dictinary `.u.w` up to now has been something like:

	q)\d .u
	q.u)`trade`quote!(7 8i;7 9 10i)
	trade| 7 8i
	quote| 7 9 10i

i.e. a dictionary of table names to values. In this exercise we are going to add table & sym level subscriptions, so that our `.u.w `might look something like:

	q.u)`trade`quote!(((7i;`);(8i;`JPM`GE);(9i;enlist`IBM));enlist(9i;enlist`IBM))
	trade| ((7i;`);(8i;`JPM`GE);(9i;,`IBM))
	quote| ,(9i;,`IBM)

Instead of a vector of handles as the values, what we will now have is a list of two item lists, each containing:

	(handle;sym(s) or `)

with ` meaning 'give me all syms'.

# Task

	/ schema.q
	trade:flip`time`sym`price!"psf"$\:()
	quote:flip`time`sym`bid`ask!"psff"$\:()
	
Here is the new tickerplant code that should form the basis for this and subsequent tp exercises in this course. 

	\l schema.q
	
	\d .u
	t:tables`.
	w:t!()
	
	/ delete subscription
	/ x=table name, y=handle
	del:{
		/ ...
	 }
	
	/ select from table (only give subscriber what they want)
	/ x=table data, y=sym(s) or `
	sel:{
		/ ...
	 }
	 
	/ add subscription
	/ x=table name, y=sym(s) or `
	add:{
		/ ...
	 }

	pub:{[t;x]{[t;x;w]if[count x:sel[x]w 1;neg[first w](`upd;t;x)]}[t;x]each w t}

	sub:{if[x~`;:sub[;y]each t];if[not x in t;'x];del[x;.z.w];add[x;y]}
	
	pc:{del[;x]each t}
	
	end:{neg[union/[w[;;0]]]@\:(`.u.end;x)}
	
	upd:{[t;x]
		c:key flip get t;	/ same as calling cols, but works from a child namespace
		pub[t;$[0>type first x;enlist;flip]c!x];
		}
	
	\d .
	
	.event.add[`.z.pc;`.u.pc];
	
The task is to fill in the `del`, `sel` and `add` functions in such a way that the tickerplant works correctly.