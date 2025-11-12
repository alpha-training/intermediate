## ctil

kdb+ has a large array of interfaces with other languages, but its interface with c is different, because q is written in c.

This presents the possibility of *extending* q with our own c functions, almost like broadening the core of q in a way that is not possible with other languages.

#Task

The task at hand is to write a c implementation of `til`:

	q)ctil:`myc 2:(`ctil;1)
	q)til[5]~ctil 5
	1b

In your solution, places all your code in a folder `c`, under which can be the various files (`.so`, `.h`., `.c` etc.), along with a file `test.q` which loads in what it needs to and compares `til` to `ctil`.