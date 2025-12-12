# Expand

The goal is to write a function that expands variables as in bash, with reference to `.conf`:

	q).conf.data:"/disk01/dev"
	q).conf.name:"tp0"
	q)expand"test"
	"test									/ no effect, as no $
	q)expand"$data"
	"/disk01/dev"							
	q)expand"$data/procs/$name"
	"/disk01/dev/procs/tp0"
	q)expand"$data/procs/$name/$client"
	"/disk01/dev/procs/tp0/MISSING"
	

**Note:** For simplicity, assume that all variables will be strings.