# Query table #1

The task at hand is to write a function called `queryTable1`:

	$ cat queryTable1.q
	
	/ t(able), c(onstraint)(s)
	queryTable1:{[t;c]
	  ..
	  ..
	 }

The function should:

* Check if **date** is a column in t
* -> If it is, it should throw an error if the date constraint is not present
* -> If it's not, it should drop any date constraints from c if present
* Run the functional select on the table, returning all columns, without a by clause

## Testing

Useful snippet:

	/ queryTable1.q
	N:10
	trade:([]date:2025.11.01+asc N?N;sym:N?`JPM`GE`IBM;size:100*1+N?10)

The function should work in all the following scenarios:

| Date Column | Date Constraint | Result| 
|---------|---------|---|
| No | No | Runs |
| No | Yes | Dynamically drop the date constraint|
| Yes | No | Throw error |
| Yes | Yes | Runs

Example:

	$ q queryTable1.q
	
	q)trade
	date       sym size
	-------------------
	2025.11.02 IBM 900 
	2025.11.02 JPM 200 
	2025.11.03 GE  1000
	2025.11.03 GE  600 
	2025.11.05 IBM 500 
	2025.11.05 JPM 700 
	2025.11.06 GE  700 
	2025.11.08 IBM 200 
	2025.11.09 GE  900 
	2025.11.09 IBM 600 
	
	/ Date column - No date constraint
	q)queryTable[trade;enlist(=;`sym;enlist`JPM)]
	'A date constraint must be present

	/ Date columnn, date constraint
	q)queryTable[trade;((in;`date;2025.11.02 2025.11.03);(=;`sym;enlist`JPM))]
	date       sym size
	-------------------
	2025.11.02 JPM 200 
	
	/ No date column, date constraint -> drop the date constraint
	q)queryTable[delete date from trade;((in;`date;2025.11.02 2025.11.03);(=;`sym;enlist`JPM))]
	sym size
	--------
	JPM 200 
	JPM 700 
	
	/ No date column, no date constraints -> runs ok
	q)queryTable[delete date from trade;enlist(=;`sym;enlist`JPM)]
	sym size
	--------
	JPM 200 
	JPM 700 
	