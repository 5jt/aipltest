Functions
=========

This document describes the APL functions to be produced and tested.

Datatype tests
--------------

	isDict       whether ⍵ is a dictionary
      isObj        whether ⍵ is an object
	isString     whether ⍵ is a string
	isStrings    whether ⍵ is strings (returns scalar Boolean; ⍵ may be vector or matrix)
	isTable      whether ⍵ is a table
	isXref       whether ⍵ is an xref


An object here is a namespace containing only variables.
Each variable’s value must itself be a variable (simple value) or another namespace (object).


Prototype of an array
---------------------

      pt           prototype of an array

Indexing with `at` returns *outrange values* instead of signalling an error.

Outrange values are derived from the prototype of their first item: 0 for numerics, space for character.

```apl
      0 ≡ pt ⍳3
1
      ' ' ≡ pt 'abc'
1
```

For this, we enclose an array *along its leading axis*, reshape to length 0, and disclose to reveal its prototype.

```apl
{⊃0⍴⊂⍤(0⌈r-1)⊢⍵}
```


Indexing with `at`
------------------

	at           extract values from dictionary, object, table, xref or array

**Purpose**: Extract values from structured data types, preserving the shape of the index argument.

Syntax: `values ← X at Y`

Where:
-   `X` is a dictionary, object, table, xref, or array
-   `Y` is an index or array of indices
-   `values` has the same shape as `Y` (with exceptions noted for tables)

The function returns only the values, not the structure. For extracting substructures that preserve the data type, use `select`.

### Index error behavior

`at` signals an INDEX ERROR for out-of-range indices or invalid keys/names, **except** for dictionaries that have an explicit outrange value (N+1 element in the values vector).

Apart from dictionaries with an explicit default, outrange indices always signal INDEX ERROR unless wrapped with `outr`, which substitutes implicit prototype defaults instead of errors.

For handling outrange values without errors, use the `outr` operator (described below).


### Dictionary `X`

A dictionary is a 2-element vector `(values ⋄ keys)` where:
-   `values` is a vector with N or N+1 elements (if N+1, the last element is the default for outrange keys)
-   `keys` is a vector with N elements (strings or integers)

**Shape preservation**: The shape of `Y` determines the shape of the result.

Examples:

```apl
      d ← (12 23 34 ⋄ 'cow' 'sheep' 'dog')
      23 ≡ d at 'sheep'  ⍝ scalar key → scalar value
1
      23 12 ≡ d at 'sheep' 'cow'  ⍝ vector keys → vector values
1
      (2 2⍴23 12 34 23) ≡ d at 2 2⍴'sheep' 'cow' 'dog' 'sheep'  ⍝ matrix keys → matrix values
1
```

Invalid keys signal INDEX ERROR:

```apl
      d ← (12 23 34 ⋄ 'cow' 'sheep' 'dog')
      d at 'cat'
INDEX ERROR
      d at 'cat'
        ^
```

With explicit default (N+1 element), invalid keys return the default instead:

```apl
      e ← (12 23 34 99 ⋄ 'cow' 'sheep' 'dog')
      99 ≡ e at 'cat'
1
      23 99 ≡ e at 'sheep' 'cat'
1
```


### Object `X`

An object is a namespace containing only variables.

**Shape preservation**: The shape of `Y` (variable names) determines the shape of the result.

Examples:

```apl
      obj ← (cow:12 ⋄ dog:34 ⋄ sheep:23)
      23 ≡ obj at 'sheep'  ⍝ scalar name → scalar value
1
      23 12 ≡ obj at 'sheep' 'cow'  ⍝ vector names → vector values
1
      (2 2⍴23 12 34 23) ≡ obj at 2 2⍴'sheep' 'cow' 'dog' 'sheep'  ⍝ matrix names → matrix values
1
```

Invalid variable names signal INDEX ERROR:

```apl
      obj at 'cat'
INDEX ERROR
      obj at 'cat'
        ^
```


### Table `X`

A table is a 2-element vector `(values ⋄ columnNames)` where `values` is a matrix and `columnNames` is a vector of strings.

`Y` can be:
1.  **Row indices** (integers): Returns rows from the values matrix
2.  **Column names** (strings): Returns columns from the values matrix
3.  **Both** `(rowIndices ⋄ columnNames)`: Returns cells from the intersection

**Row indexing**:
-   Scalar index: returns a vector (one row of values)
-   Vector index: returns a matrix (selected rows, all columns)
-   Higher rank: each position contains a row vector

**Column indexing**:
-   Scalar: returns a vector (one column of values)
-   Vector: returns a matrix (all rows, selected columns)

**Combined indexing** `(rows ⋄ cols)`:
-   `(scalar ⋄ scalar)`: returns scalar value
-   `(scalar ⋄ vector)`: returns vector of values
-   `(vector ⋄ scalar)`: returns vector of values
-   `(vector ⋄ vector)`: returns matrix of values

Examples:

```apl
      t ← (['Bob' 21 'blue' ⋄ 'Ted' 32 'green' ⋄ 'Carol' 43 'brown'] ⋄ 'name' 'age' 'eye')

      ⍝ Row indexing
      'Ted' 32 'green' ≡ t at 2  ⍝ scalar → vector (one row)
1
      (1 3⍴'Ted' 32 'green') ≡ t at ,2  ⍝ 1-row matrix
1
      (2 3⍴'Carol' 43 'brown' 'Bob' 21 'blue') ≡ t at 3 1  ⍝ 2 rows
1

      ⍝ Column indexing
      21 32 43 ≡ t at 'age'  ⍝ returns the age column
1
      (3 2⍴'blue' 'Bob' 'green' 'Ted' 'brown' 'Carol') ≡ t at 'eye' 'name'
1

      ⍝ Combined indexing
      32 ≡ t at (2 ⋄ 'age')  ⍝ scalar row, scalar col → scalar value
1
      32 'green' ≡ t at (2 ⋄ 'age' 'eye')  ⍝ scalar row, vector cols → vector
1
      21 43 ≡ t at (1 3 ⋄ 'age')  ⍝ vector rows, scalar col → vector
1
      (2 2⍴21 'blue' 43 'brown') ≡ t at (1 3 ⋄ 'age' 'eye')  ⍝ vector×vector → matrix
1
```

Out-of-range row indices signal INDEX ERROR:

```apl
      t at 4
INDEX ERROR
      t at 4
        ^
```

Invalid column names signal INDEX ERROR:

```apl
      t at 'sex'
INDEX ERROR
      t at 'sex'
        ^
```


### Xref `X`

An xref is a 3-element vector `(values ⋄ columnNames ⋄ rowNames)` where `values` is a matrix and both `columnNames` and `rowNames` are vectors of strings.

`Y` is `(columnNames ⋄ rowNames)` where each can be scalar or vector strings.

Shape behavior follows the outer product of row and column index shapes:
-   `(scalar ⋄ scalar)`: returns scalar value
-   `(scalar ⋄ vector)`: returns vector
-   `(vector ⋄ scalar)`: returns vector
-   `(vector ⋄ vector)`: returns matrix

Examples:

```apl
      x ← ([21 'blue' ⋄ 32 'green' ⋄ 43 'brown'] ⋄ 'age' 'eye' ⋄ 'Bob' 'Ted' 'Carol')

      21 ≡ x at ('age' ⋄ 'Bob')  ⍝ scalar × scalar → scalar
1
      21 'blue' ≡ x at ('age' 'eye' ⋄ 'Bob')  ⍝ vector × scalar → vector
1
      21 32 ≡ x at ('age' ⋄ 'Bob' 'Ted')  ⍝ scalar × vector → vector
1
      (2 2⍴43 'brown' 21 'blue') ≡ x at ('age' 'eye' ⋄ 'Carol' 'Bob')  ⍝ vector × vector → matrix
1
```

Invalid row or column names signal INDEX ERROR:

```apl
      x at ('sex' ⋄ 'Bob')
INDEX ERROR
      x at ('sex' ⋄ 'Bob')
        ^
```


### Array `X`

If `X` is none of the above structured datatypes, it is just an array.

`at` indexes along the leading axis.

Examples:

```apl
      'abc' ≡ 'abcdef' at 1 2 3
1
```

Out-of-range indices signal INDEX ERROR:

```apl
      (10 20 30) at 1 2 4
INDEX ERROR
      (10 20 30) at 1 2 4
                ^
```


### Scalar `X`

If `X` has no dimensions, signal a RANK ERROR.


Selecting with `select`
-----------------------

	select       extract substructure from dictionary, object, table, or xref

**Purpose**: Extract substructures maintaining the original data type.

Syntax: `subset ← X select Y`

Where:
-   `X` is a dictionary, object, table, or xref
-   `Y` specifies which elements to select
-   `subset` has the same type as `X`

Unlike `at`, which returns values, `select` returns a structure of the same type as the input.

### Index error behavior

`select` signals an INDEX ERROR for out-of-range indices or invalid keys/names, **except** for dictionaries that have an explicit outrange value (N+1 element in the values vector).

Apart from dictionaries with an explicit default, outrange indices always signal INDEX ERROR unless wrapped with `outr`, which substitutes implicit prototype defaults instead of errors.

For handling outrange values without errors, use the `outr` operator (described below).


### Dictionary

Returns a dictionary containing only the selected keys and their values.

```apl
      d ← (12 23 34 ⋄ 'cow' 'sheep' 'dog')
      (23 34 ⋄ 'sheep' 'dog') ≡ d select 'sheep' 'dog'
1
```

If the source dictionary has an explicit default value (N+1 values), the selected dictionary retains that default unchanged, even when `Y` contains only in-range keys.


### Object

Returns a new namespace containing only the selected variables.

```apl
      obj ← (cow:12 ⋄ dog:34 ⋄ sheep:23)
      obj2 ← obj select 'sheep' 'dog'
      ⍝ obj2 is a namespace containing only sheep:23 and dog:34
```

Objects follow APL’s `⎕NL` alphabetical ordering; `key` and `value` reflect this ordering.


### Table

`Y` can be:
-   Row indices (integers, scalar or vector): returns table with those rows
-   Column names (strings, scalar or vector): returns table with those columns
-   `(rowIndices ⋄ columnNames)`: returns table with selected rows and columns

```apl
      t ← (['Bob' 21 'blue' ⋄ 'Ted' 32 'green' ⋄ 'Carol' 43 'brown'] ⋄ 'name' 'age' 'eye')

      ⍝ Select rows
      (['Bob' 21 'blue' ⋄ 'Carol' 43 'brown'] ⋄ 'name' 'age' 'eye') ≡ t select 1 3
1

      ⍝ Select columns
      (['Bob' 21 ⋄ 'Ted' 32 ⋄ 'Carol' 43] ⋄ 'name' 'age') ≡ t select 'name' 'age'
1

      ⍝ Select both
      (['Bob' 21 ⋄ 'Carol' 43] ⋄ 'name' 'age') ≡ t select (1 3 ⋄ 'name' 'age')
1
```

Row and column selections preserve the order and duplicates provided in `Y`. Under `select outr`, outrange rows are filled with prototype rows in the requested order; invalid column names still signal INDEX ERROR.


### Xref

Returns an xref with selected rows and columns.

```apl
      x ← ([21 'blue' ⋄ 32 'green' ⋄ 43 'brown'] ⋄ 'age' 'eye' ⋄ 'Bob' 'Ted' 'Carol')
      ([21 ⋄ 43] ⋄ ⊂'age' ⋄ 'Bob' 'Carol') ≡ x select (⊂'age' ⋄ 'Bob' 'Carol')
1
```

Row and column indices must be scalar or vector. Selections preserve the order and duplicates provided in `Y`. Under `select outr`, outrange rows or columns are filled with prototype rows/columns in the requested order.


Handling outrange values with `outr`
-------------------------------------

	outr         operator that wraps `at` or `select` to handle outrange values

**Purpose**: Enable `at` or `select` to return outrange values instead of signaling INDEX ERROR.

Syntax: `result ← X (F outr) Y`

Where:
-   `X` is a dictionary, object, table, xref, or array
-   `F` is `at` or `select`
-   `Y` is an index or array of indices
-   `result` is what `F` would return, with outrange values substituted for invalid indices

The `outr` operator calculates appropriate outrange values for the left argument `X` and modifies both `X` and the indices `Y` so that `at` or `select` can return results without signaling errors.


### How outrange values are calculated

**Dictionary**:
-   If the dictionary has N+1 elements in its values vector, the (N+1)th element is the explicit outrange value
-   Otherwise, use the prototype of the values: `{∊⍵⊣⎕ML←0}0⍴values`

**Object**:
-   Use the prototype of the alphabetically first variable's value
-   For an empty object, use `''`

**Table**:
-   For out-of-range rows: calculate the prototype for each column using `{⊃0⍴⊂⍤(0⌈r-1)⊢⍵}` where `r` is the rank of that column
-   Invalid column names signal INDEX ERROR (cannot be handled by `outr`)

**Xref**:
-   For invalid row names: calculate prototypes for each column as with tables
-   For invalid column names: calculate prototypes for each row similarly
-   The intersection of invalid row and column uses the overall prototype

**Array**:
-   Use the prototype of the array: `{⊃0⍴⊂⍤(0⌈r-1)⊢⍵}` where `r` is the rank


### Examples

Dictionary with implicit default:

```apl
      d ← (12 23 34 ⋄ 'cow' 'sheep' 'dog')
      0 ≡ d (at outr) 'cat'  ⍝ prototype of numeric values
1
      23 0 ≡ d (at outr) 'sheep' 'cat'
1
```

Dictionary with explicit default:

```apl
      e ← (12 23 34 99 ⋄ 'cow' 'sheep' 'dog')
      99 ≡ e (at outr) 'cat'
1
      23 99 ≡ e (at outr) 'sheep' 'cat'
1
```

Object:

```apl
      obj ← (cow:12 ⋄ dog:34 ⋄ sheep:23)
      0 ≡ obj (at outr) 'cat'  ⍝ prototype from first variable (cow)
1
```

Table with out-of-range rows:

```apl
      t ← (['Bob' 21 'blue' ⋄ 'Ted' 32 'green' ⋄ 'Carol' 43 'brown'] ⋄ 'name' 'age' 'eye')
      '   ' 0 '    ' ≡ t (at outr) 4  ⍝ prototypes for each column
1
      (2 3⍴'   ' 0 '    ' 'Bob' 21 'blue') ≡ t (at outr) 4 1
1
```

Array:

```apl
      0 ≡ (10 20 30) (at outr) 4
1
      10 20 0 ≡ (10 20 30) (at outr) 1 2 4
1
```


Extracting components with `key` and `value`
---------------------------------------------

	key          extract keys/names from dictionary, object, table, or xref
	value        extract values from dictionary, object, table, or xref

**Purpose**: Extract the component parts of structured data types.


### `key` - Extract keys/names

Syntax: `keys ← key X`

Returns:
-   **Dictionary**: the keys vector
-   **Object**: variable names (sorted alphabetically)
-   **Table**: column names
-   **Xref**: `(columnNames ⋄ rowNames)`

Examples:

```apl
      d ← (12 23 34 ⋄ 'cow' 'sheep' 'dog')
      'cow' 'sheep' 'dog' ≡ key d
1

      obj ← (cow:12 ⋄ dog:34 ⋄ sheep:23)
      'cow' 'dog' 'sheep' ≡ key obj  ⍝ alphabetically sorted
1

      t ← (['Bob' 'Ted' ⋄ 21 32 ⋄ 'blue' 'green'] ⋄ 'name' 'age' 'eye')
      'name' 'age' 'eye' ≡ key t
1

      x ← ([21 'blue' ⋄ 32 'green'] ⋄ 'age' 'eye' ⋄ 'Bob' 'Ted')
      ('age' 'eye' ⋄ 'Bob' 'Ted') ≡ key x
1
```


### `value` - Extract values

Syntax: `vals ← value X`

Returns:
-   **Dictionary**: the values vector (N elements, excluding default if present)
-   **Object**: variable values in alphabetical order by name
-   **Table**: the values matrix
-   **Xref**: the values matrix

Examples:

```apl
      d ← (12 23 34 ⋄ 'cow' 'sheep' 'dog')
      12 23 34 ≡ value d
1

      d2 ← (12 23 34 99 ⋄ 'cow' 'sheep' 'dog')  ⍝ with default
      12 23 34 ≡ value d2  ⍝ excludes the default
1

      obj ← (cow:12 ⋄ dog:34 ⋄ sheep:23)
      12 34 23 ≡ value obj  ⍝ values in alphabetical order by name
1

      t ← (['Bob' 'Ted' ⋄ 21 32 ⋄ 'blue' 'green'] ⋄ 'name' 'age' 'eye')
      (['Bob' 'Ted' ⋄ 21 32 ⋄ 'blue' 'green']) ≡ value t
1

      x ← ([21 'blue' ⋄ 32 'green'] ⋄ 'age' 'eye' ⋄ 'Bob' 'Ted')
      ([21 'blue' ⋄ 32 'green']) ≡ value x
1
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
