PLAN
====

We proceed in phases.


## 1. Datatype functions and minimal test framework

Draft only the datatype tester [functions](FUNCTIONS.md) and write tests for them.
There are drafts in the `fun` folder.

The minimal [TinyTest](https://github.com/5jt/tinytest) tools might suffice for testing them.

Draft the functions and the tests and seek approval.


## 2. String interpolation

Draft the `f` function, write tests for it and seek approval.

The `f` function traps execution errors, so the TinyTest `catch` operator should nt be needed.

## 3. Simple string manipulation

Draft function `j` and simple versions of `p` and `r`. In these simple versions 

-   the left argument of `p` is a single character
-   the left argument of `r` is a 2-character string

Make the functions robust: signal a domain error (`⎕SIGNAL 11`) if the right argument is not a string or the left argument is invalid.

Draft tests and seek approval.

## 4. Full string manipulation

Extend the left arguments of `p` and `r` to strings of any length. This probably involves using `⎕R` and/or `⎕S`. (The new algorithms might even be comparably or more efficient than the earlier algorithms for the simple Phase 3 left arguments.)

## 5. Index dictionaries

## 6. Index tables

## 7. Index Xrefs