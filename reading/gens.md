# genS

This task is a modification of `genSyms`. Instead of returning syms of the number specified by the arg, `genS` should return all the sym combinations up to that point:

	q)genS 1
	`A`B`C`D`E`F`G`H`I`J`K`L`M`N`O`P`Q`R`S`T`U`V`W`X`Y`Z
	q)genS 2
	`A`B`C`D`E`F`G`H`I`J`K`L`M`N`O`P`Q`R`S`T`U`V`W`X`Y`Z`AA`AB`AC`AD`AE`..
	q)-3#genS 2
	`ZX`ZY`ZZ
	
Any we can double check that a sym of length <= the argument is always present e.g.

	q)`GE`JPM`MSFT in genS 3
	110b