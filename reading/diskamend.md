# Disk amend

Write the function `diskAmend`:

	q)`:L set til 10
	`:L
	q)diskAmend[`:L;4;100]
	`:L
	q)diskAmend[`:L;3 5;1000]
	`:L

**Note**: your function may require logic to ensure that the indices are turned into list form if the caller passes an atomic index.