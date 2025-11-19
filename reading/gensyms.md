Using `.Q.A`, write the function `genSyms` that behaves as follows:

	q)q)genSyms 1
	`A`B`C`D`E`F`G`H`I`J`K`L`M`N`O`P`Q`R`S`T`U`V`W`X`Y`Z
	q)genSyms 2
	`AA`AB`AC`AD`AE`AF`AG`AH`AI`AJ`AK`AL`AM`AN`AO`AP..
	q)-5#genSyms 2
	`ZV`ZW`ZX`ZY`ZZ
	q)genSyms 5
	`AAAAA`AAAAB`AAAAC`AAAAD`AAAAE`AAAAF`AAAAG`AAAAH..
	q)-4#genSyms 5
	`ZZZZW`ZZZZX`ZZZZY`ZZZZZ
	q)count genSyms 5
	11881376

To test against, `genSyms2` may be found in the `intermediate-alf` repo.