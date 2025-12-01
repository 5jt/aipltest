Test file format
================

Test files use Dyalog APL Array Notation (APLA format) to define test cases for APL functions.

## File location

Test files are stored in `tests/` with `.apla` extension, named after the function being tested:
- `tests/isDict.apla`
- `tests/isString.apla`
- `tests/j.apla`
- etc.

## File structure

Each APLA file contains a vector of test objects (namespaces) enclosed in parentheses.
Function name is derived from the filename.

## Test object structure

Each test object (namespace) has:
- **description** - Test description (string)
- **expected** - Expected result
- **rarg** - Right argument (test input)
- **larg** - Left argument (optional; presence indicates dyadic test)

Test objects use namespace syntax: `field:value`

### Example: isString.apla (monadic only)

```apl
(
 (
  description:'simple string'
  expected:1
  rarg:'hello'
 )
 (
  description:'empty string'
  expected:1
  rarg:''
 )
 (
  description:'numeric vector'
  expected:0
  rarg:1 2 3
 )
 (
  description:'empty numeric vector'
  expected:0
  rarg:⍬
 )
)
```

### Example: j.apla (dyadic only)

```apl
(
 (
  description:'join with space'
  larg:' '
  expected:'abc def'
  rarg:('abc'⋄)('def'⋄)
 )
 (
  description:'join with dash'
  larg:'-'
  expected:'abc-def'
  rarg:('abc'⋄)('def'⋄)
 )
 (
  description:'join with multi-char separator'
  larg:'::'
  expected:'a::b::c'
  rarg:('a'⋄)('b'⋄)('c'⋄)
 )
)
```

### Example: p.apla (ambivalent - mixed monadic and dyadic)

```apl
(
 (
  description:'default space separator (monadic)'
  expected:('quick'⋄)('brown'⋄)('fox'⋄)
  rarg:'quick brown fox'
 )
 (
  description:'partition on slash (dyadic)'
  larg:'/'
  expected:('path'⋄)('to'⋄)('file'⋄)
  rarg:'path/to/file'
 )
 (
  description:'partition on dash (dyadic)'
  larg:'-'
  expected:('a'⋄)('b'⋄)('c'⋄)
  rarg:'a-b-c'
 )
)
```

## Array Notation features

Array Notation properly represents APL data structures:
- **Matrices**: `[row1⋄row2⋄...]` notation
- **Strings**: Character vectors like `'hello'`
- **Nested vectors**: `('abc'⋄)('def'⋄)` for strings vector
- **Empty numeric vector**: `⍬` (zilde)
- **Scalars and vectors**: Direct notation `42`, `1 2 3`

## Test runner interpretation

The test runner (`test/Run.aplf`) will:
1. Load each `.apla` file from `tests/` directory using `⎕VGET`
2. Extract function name from filename
3. For each test object:
   - Check for `larg` field to determine if dyadic
   - Execute function monadically or dyadically as appropriate
   - Compare result with `expected` value
4. Accumulate error counts (pass, fail, broke)
5. Write detailed results to `logs/{function}.log`
6. Print summary table
