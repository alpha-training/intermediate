# Tickerplant V0.3
The next modification we want to make to our tickerplant is *timestamping*.

Depending on the feed, we may not receive a time as the first column, so a standard tickerplant will basically attach the timestamps and make it the first column.

## Task
Copy `tp2.q` to `tp3.q` and add logic sto your `.u.upd` that looks something like:

	if[not -12h=type first first x;
		/ ...
		];
	
If it's working correctly:

* If the first column is not of type timestamp, add it. If already there, do nothing
* Both the log file and the subscribers should always receive data with the first column of type timestamp


These this out by changing your data generation to not have a timestamp column first.
