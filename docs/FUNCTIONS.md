Functions
=========

This document describes the APL functions to be produced and tested.

Datatype tests
--------------

	isDict       whether ⍵ is a dictionary
	isString     whether ⍵ is a string
	isStrings    whether ⍵ is strings (returns scalar Boolean; ⍵ may be vector or matrix)
	isTable      whether ⍵ is a table
	isXref       whether ⍵ is an xref

Indexing with `at`
------------------

	at           indexes dictionary, object, table or xref (c.f. @ in q language)

Syntax: `sel ← X at Y`

Functional indexing: where

-   `X` is a dictionary, table or xref
-   `Y` is a key or keys to `X`

return `sel`, a selection of values from `X`, as follows:

### Dictionary `X`:

A dictionary is a 2-element vector `(values ⋄ keys)` where:
- `values` is a vector with N or N+1 elements (if N+1, the last element is the default for outrange keys)
- `keys` is a vector with N elements (strings or integers)

`Y` is one or more keys to `X`; `sel` is the corresponding values with the same shape as `Y`, e.g.

```apl
      d ← (12 23 34 ⋄ 'cow' 'sheep' 'dog')
      23 ≡ d at 'sheep'  ⍝ scalar key → scalar result
1
      23 12 ≡ d at 'sheep' 'cow'  ⍝ vector keys → vector result
1
      (2 2⍴23 12 34 23) ≡ d at 2 2⍴'sheep' 'cow' 'dog' 'sheep'  ⍝ matrix keys → matrix result
1
```

Outrange examples:

```apl
      d ← (12 23 34 ⋄ 'cow' 'sheep' 'dog')
      0 ≡ d at 'cat' ⍝ ⊃0⍴⊃⊃d
1
      23 0 ≡ d at 'sheep' 'cat'
1
      e ← (12 23 34 99 ⋄ 'cow' 'sheep' 'dog') ⍝ 99 is default
      99 ≡ d at 'cat'
1
      23 99 ≡ d at 'sheep' 'cat'
1
```

### Table `X`:

`sel` is a table or dictionary. `Y` is one of:

1.  integer vector | `X{ (v k)←⍺ ⋄ (v[⍵;] ⋄ k) }Y`
2.  strings        | `X{ (v k)←⍺ ⋄ (v[;k⍳⍵] ⋄ ⍵) }Y`
3.  both the above

In the last case the table returned has the selected rows and columns of `X`.

If the row selection in (1) or (3) is a scalar, then the result is a dictionary, not a table. (A table is equivalent to a list of dictionaries.)

Examples:

```apl
      ⊢t ← (⍉['Bob' 'Ted' 'Carol' ⋄ 21 32 43 ⋄ 'blue' 'green' 'brown'] ⋄ 'name' 'age' 'eye')
┌────────────────┬──────────────┐
│┌─────┬──┬─────┐│┌────┬───┬───┐│
││Bob  │21│blue │││name│age│eye││
│├─────┼──┼─────┤│└────┴───┴───┘│
││Ted  │32│green││              │
│├─────┼──┼─────┤│              │
││Carol│43│brown││              │
│└─────┴──┴─────┘│              │
└────────────────┴──────────────┘
      ⍝ indexing with integers
      t at 2 ⍝ dictionary, not table
┌──────────────┬──────────────┐
│┌───┬──┬─────┐│┌────┬───┬───┐│
││Ted│32│Green│││name│age│eye││
│└───┴──┴─────┘│└────┴───┴───┘│
└──────────────┴──────────────┘
      ≢⍴⊃t at 2 ⍝ dictionary, not table
1
      t at ,2 ⍝ table, not dictionary
2
      t at 3 1
┌────────────────┬──────────────┐
│┌─────┬──┬─────┐│┌────┬───┬───┐│
││Carol│43│brown│││name│age│eye││
│├─────┼──┼─────┤│└────┴───┴───┘│
││Bob  │21│blue ││              │
│└─────┴──┴─────┘│              │
└────────────────┴──────────────┘

      ⍝ indexing with strings returns table
      t at 'eye' 'name'
┌──────────┬──────────┐
│┌─────┬──┐│┌───┬────┐│
││blue │21│││eye│name││
│├─────┼──┤│└───┴────┘│
││green│32││          │
│├─────┼──┤│          │
││brown│43││          │
│└─────┴──┘│          │
└──────────┴──────────┘
      ⍝ indexing with string returns 1 column
      t at 'age'
21 32 43

      ⍝ indexing with integers and strings
      t at (3 1 ⋄ 'eye' 'name')
┌─────────────┬──────────┐
│┌─────┬─────┐│┌───┬────┐│
││brown│Carol│││eye│name││
│├─────┼─────┤│└───┴────┘│
││blue │Bob  ││          │
│└─────┴─────┘│          │
└─────────────┴──────────┘
```

Outrange rows are represented in the result by `⊃0⍴` on each cell:

```apl
      t at 4 1
┌─────────────┬──────────────┐
│┌───┬──┬────┐│┌────┬───┬───┐│
││   │0 │    │││name│age│eye││
│├───┼──┼────┤│└────┴───┴───┘│
││Bob│21│blue││              │
│└───┴──┴────┘│              │
└─────────────┴──────────────┘
```

Outrange columns signal an index error:

```apl
      t at 'age' 'sex'
INDEX ERROR
      t at 'age' 'sex'
        ^
```

### Xref `X`

An xref is a matrix with string keys for both axes.
The result `sel` is an

-   xref if each index is strings
-   a row or column of the matrix if one index is a string
-   a cell of the matrix if each index is a string

Example:

```apl
      ⊢x←(⍉[21 32 43 ⋄ 'blue' 'green' 'brown'] ⋄ 'Bob' 'Ted' 'Carol' ⋄ 'name' 'age' 'eye')
┌──────────┬───────────────┬─────────┐
│┌──┬─────┐│┌───┬───┬─────┐│┌───┬───┐│
││21│blue │││Bob│Ted│Carol│││age│eye││
│├──┼─────┤│└───┴───┴─────┘│└───┴───┘│
││32│green││               │         │
│├──┼─────┤│               │         │
││43│brown││               │         │
│└──┴─────┘│               │         │
└──────────┴───────────────┴─────────┘
      ⍝ each index is strings
      x at ('Carol' 'Bob' ⋄ 'eye' 'age')
┌──────────┬─────────┐
│┌─────┬──┐│┌───┬───┐│
││brown│43│││eye│age││
│├─────┼──┤│└───┴───┘│
││blue │21││         │
│└─────┴──┘│         │
└──────────┴─────────┘
      ⍝ one index is a string
      x at ('Carol' 'Bob' ⋄ 'age')
43 21
      ⍝ each index is a string
      x at ('Bob' ⋄ 'age')
21
```

String manipulation
-------------------

	j            join strings ⍺ with separator ⍵
	p            partition string ⍵ on scalar separator ⍺ (default ' ')
	r            `(o n) r s`: replace characters `o` in string `s` with char `n`

### Join strings

Syntax: `Z ← [X] j Y`

Where  `Y` is a string or strings and `X` is an optional string (defaults to `' '`) `Z` is a string 

```apl
Y[1],X,Y[2],X,Y[3] ...
```

Examples:
```apl
      'a b c' ≡ j 'abc'
1
      'a-b-c' ≡ '-' j 'abc'
1
      'abc def' ≡ j 'abc' 'def'
1
      'abc-def' ≡ '-' j 'abc' 'def'
1
      ≢j ''
0
```

### Partition string

Syntax: `Z ← [X] p Y`

Where `Y` is a string and `x` is an optional string (defaults to `' '`), `Z` is `Y` partitioned on ocurrences of `X`.

Examples:
```apl
      'quick' 'brown' 'fox' ≡ p 'quick brown fox'
1
      'path' 'to' 'file' ≡ '/' p 'path/to/file'
1
      'path' 'to' 'file' ≡ '//' p 'path//to//file'
1
      ≢ '//' p ''
0
```

Implementation tip: where `X` is a single character, `X(≠⊆⊢)Y` suffices; otherwise `⎕R` or `⎕S` may be needed. 

### String replace

Syntax: `Z ← (old ⋄ new) r Y`

Where `Y`, `old` and `new` are strings, `Z` is `Y` with every occurrence of `old` replaced by `new`.

Examples:

```apl
      'my-file.txt' ≡ ' -' r 'my file.txt'
1
      'path/to/file' ≡ ('//' ⋄ '/') r 'path//to//file'
1
      ≢ ('//' ⋄ '/') r ''
0
```

Implementation tip: Where `old` and `new` are both single characters, `n@(⍸⍵=o)⊢⍵` suffices. Otherwise, `⎕S` is required.


String interpolation with `f`
-----------------------------

Emulates Python string interpolation. 
Function `f` has syntax:

      Z ← [ns|values] f str

String `str` may contain executable expressions embraced with curly braces prefixed with a dollar sign, e.g.

    'My luv is like a ${colour}, ${colour} ${flower} in ${season}.'
    'The quick, brown ${1} jumps over the lazy ${2}'

**In the first use** `ns` is an optional namespace; it defaults to the environment in which `f` is called. Escaped expressions are evaluated in this namespace; if successful, the results are formatted with `⍕` and substituted in the string.

```apl
      (flower colour)←'rose' 'red'
      ×⎕NC 'colour' 'flower' 'season'
1 1 0
       f'My luv is like a ${colour}, ${colour} ${flower} in ${season}.'
My luv is like a red, red rose in ${season}.
       ns←(colour:'pink' ⋄ flower:'pansy')
       ns f'My luv is like a ${colour}, ${colour} ${flower} in ${season}.'
My luv is like a pink, pink pansy in ${season}.
```

Where the left argument is a list of values, each escaped expressions must be an integer in the range `⍳≢values`: it is replaced by the corresponding item of `values`, also formatted.

```apl
      'dog' 'fox'f'The quick, brown ${2} jumps ${3} the lazy ${1}.'
The quick, brown fox jumps ${3} the lazy dog.
```

