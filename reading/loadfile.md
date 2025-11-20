# Load File

Write a function `loadf` that takes a q file path as its argument, and loads it in.

First create some test script:

	/ test.q
	
	A:100

And load it from a q process:

	$ q loadf.q
	
	q)loadf `:test.q
	q)A
	100		/ looks good

**Note:** To load a file programmatically, you'll need to do `system"l ",[file path]`
