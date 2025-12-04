# Last date of previous month

Given any date, return the last date of the previous month:

	q)lastDatePrevMonth 2025.12.31
	2025.11.30
	q)lastDatePrevMonth 2026.01.07
	2025.12.31
	q)lastDatePrevMonth 2026.03.30
	2026.02.28

**Tip**: Casting between type `date` and type `month` can be useful
* Chaining might be possible here