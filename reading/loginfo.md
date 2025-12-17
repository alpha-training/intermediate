# Log info

Using alf's `data/tp2025.03.01` log file to test with, write a script `loginfo.q` to produce the following table:

	q)loginfo
	table| chunks rows
	-----| -----------
	quote| 7      63  
	trade| 3      29 
	
Your file should begin as follows:

	L:`:data/tp2025.03.01
	loginfo:.....		/ table definition
	... 				/ rest of logic
	
**Note:** Do not use the `get` function in your solution. In the real world log files can be very large, and using get would load the whole file into memory.

**Note:** do something like `cp -r /path/to/alf/data .` and edit / create a `.gitignore` file in your local directory to add a `data` row. This will ensure that the data is not added to git. The .gitignore file can be committed.