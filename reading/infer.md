# infer

Write a function `infer` that automatically casts a string to the appropriate type:

	q)infer "100"
	100
	q)infer "4.5"
	4.5
	q)infer "4f"
	4f
	q)infer "2025.10m"
	2025.10m
	q)infer "2025.10.10"
	2025.10.10
	q)infer "10 20 30"			/ handle lists
	10 20 30
	q)infer "09:30 16:00"
	09:30 16:00
	
## use of get

All of the above would have functioned perfectly well with:

	q)infer:get

`infer` should use `get` to do most of the heavy lifting, with the follwing caveats.

1. strings that don't match a specific pattern, or are empty, should be returned unchanged
2. For safety reasons, qualifying patterns from `get` are numbers / dates, with an optional trailing single character e.g. `"10"` `"2.3 7.2"` `"7 8h"` `"2025.10.10"` - allow our system to run **any** string would be risky (like `"exit 0"`)
3. If the first character is `:`, cast the arg to a symbol
4. If there is a whitespace in the arg, and the test at 2) has not been met, then call yourself for each `" "vs x`
5. Drill down into complex args (complex lists, tables, dicts)

## Testing 

The following code can be used for testing.

```unit:enlist`s`result`expected!(::;::;::)
ufx:{`unit insert(enlist x;enlist infer x;enlist y);}
uf:{ufx[x;get x]}

uf"10"
uf"10f"
uf"10 20"
uf"1.5 7"
uf"5 6h"
uf"50 6f"
uf"23:59"
uf"2025.12.14"
uf"2025.12.14D20"
uf"2025.12.14D10 2025.12.14D11"
uf"`JPM"
uf"`JPM`GE"
uf"23:58 23:59"
uf"23:58:00 23:59"
ufx[":data/path";`:data/path]
ufx["data/path";"data/path"]
ufx["John123";"John123"]
ufx["exit 1";("exit";1)]
ufx[`size`price`sym!("10";"4.5";"`JPM");`size`price`sym!(10;4.5;`JPM)]
ufx[enlist`size`price`sym!("10";"4.5";"`JPM");enlist`size`price`sym!(10;4.5;`JPM)]

delete from`unit where s~'(::);

\c 200 200

if[count err:select from unit where not result~'expected;show err];