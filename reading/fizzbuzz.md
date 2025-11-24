# Fizz Buzz

Write a function `fizzBuzz` that takes a number as its argument, and returns a dictionary of 1 to N as its keys (of type long), and the following symbols as its values:

* `**Fizz** where the key is divisble by 3
* `**Buzz** where they key is divisible by 5
* `**FizzBuzz** where the key is divisible by both 3 and 5
* Otherwise return a symbol of the number

**Time to complete**: 40 minutes

```
q)fizzBuzz 30
1 | 1
2 | 2
3 | Fizz
4 | 4
5 | Buzz
6 | Fizz
7 | 7
8 | 8
9 | Fizz
10| Buzz
11| 11
12| Fizz
13| 13
14| 14
15| FizzBuzz
16| 16
17| 17
18| Fizz
19| 19
20| Buzz
21| Fizz
22| 22
23| 23
24| Fizz
25| Buzz
26| 26
27| Fizz
28| 28
29| 29
30| FizzBuzz

q)type key fizzBuzz 5
7h
q)type get fizzBuzz 5
11h