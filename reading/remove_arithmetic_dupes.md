# Remove arithmetic duplicates

Write a function `removeAD` that removes items that equal the previous item:

	q)removeAD "abca"
	"abca"		/ No change, two a's allowed
	q)removeAD "abcaab"
	"abcab"
	q)removeAD "abcaabbba"
	"abcaba"
	q)removeAD 10 20 20 30 10
	10 20 30 10	

**Note**: this is not the same as `distinct` - we are removing *sequential* duplicates - not all duplicates.

**Time to completion**: 15 minutes