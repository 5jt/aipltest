PLAN
====

We proceed in phases.


## 0. Test framework foundation

Establish the testing infrastructure before implementing features.

1. Copy [TinyTest](https://github.com/5jt/tinytest) operators (`match`, `catch`) and function (`report`) into `test/`
2. Create `test/Run.aplf` to discover and execute test files from `tests/`
3. Define test file format: tables using Array Notation (APLM format)
   - Columns: function name, left argument (optional), right argument, expected result, description
   - Each row represents one test case
4. Create `docs/TODO.md` for task tracking
5. Write a simple test file to verify the framework works
6. Document test framework design decisions

Seek approval before proceeding to Phase 1.


## 1. Datatype functions

Draft and test the datatype tester [functions](FUNCTIONS.md):
- `isDict`
- `isString`
- `isStrings`
- `isTable`
- `isXref`

Drafts exist in the `fun/` folder.

Tests should cover:
- Expected true cases for each datatype
- Expected false cases (other datatypes)
- Edge cases (empty arrays, scalars, nested structures)
- Both numeric and character data where applicable

Many APL primitive functions are not type-specific. Test the datatype functions across numeric and character data to verify they work correctly and identify any validation needs.

Draft tests and seek approval before implementing any changes to functions.

After implementation, run complete test suite to verify no regressions.


## 2. Simple string manipulation

Draft function `j` and simple versions of `p` and `r`. In these simple versions:

-   the left argument of `p` is a single character
-   the left argument of `r` is a 2-character string

Drafts exist in the `fun/` folder but may need extension.

Make the functions robust: signal a domain error (`⎕SIGNAL 11`) if the right argument is not a string or the left argument is invalid.

Draft tests and seek approval.

After implementation, run complete test suite to verify no regressions.


## 3. String interpolation

Draft the `f` function and write tests for it.

A draft exists in the `fun/` folder.

The `f` function traps execution errors internally, so the TinyTest `catch` operator should not be needed for testing error cases.

Draft tests and seek approval.

After implementation, run complete test suite to verify no regressions.


## 4. Full string manipulation

Extend the left arguments of `p` and `r` to strings of any length. This probably involves using `⎕R` and/or `⎕S`. (The new algorithms might even be comparably or more efficient than the earlier algorithms for the simple Phase 2 left arguments.)

Draft tests and seek approval.

After implementation, run complete test suite to verify no regressions.


## 5. Index dictionaries

Implement `at` function for dictionary indexing as specified in [FUNCTIONS.md](FUNCTIONS.md).

Handle:
- Single and multiple keys
- Outrange keys (return `⊃0⍴⊃⊃d` or default value if available)

Draft tests and seek approval.

After implementation, run complete test suite to verify no regressions.


## 6. Index tables

Extend `at` function for table indexing as specified in [FUNCTIONS.md](FUNCTIONS.md).

Handle:
- Integer vector (row selection, returns table)
- Scalar integer (row selection, returns dictionary)
- Strings (column selection, returns table)
- Single string (column selection, returns vector)
- Both integers and strings (row and column selection)
- Outrange rows (fill with `⊃0⍴` on each cell)
- Outrange columns (signal index error)

Draft tests and seek approval.

After implementation, run complete test suite to verify no regressions.


## 7. Index xrefs

Extend `at` function for xref indexing as specified in [FUNCTIONS.md](FUNCTIONS.md).

Handle:
- Strings for both axes (returns xref)
- String for one axis (returns row or column vector)
- String for each axis (returns scalar cell)

Draft tests and seek approval.

After implementation, run complete test suite to verify no regressions.