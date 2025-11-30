Test file format
================

Test files use JSON format to define test cases for APL functions.

## File location

Test files are stored in `tests/` with `.json` extension, named after the function being tested:
- `tests/isDict.json`
- `tests/isString.json`
- `tests/j.json`
- etc.

## File structure

Each JSON file contains:
- **function**: Name of the function being tested (string)
- **monadic**: Array of monadic test cases (optional)
- **dyadic**: Array of dyadic test cases (optional)

Ambivalent functions include both `monadic` and `dyadic` arrays.

## Monadic test structure

Each monadic test case object has:
- **rarg** - Right argument (test input)
- **expected** - Expected result
- **description** - Test description (string)

### Example: isString.json

```json
{
  "function": "isString",
  "monadic": [
    {
      "rarg": "hello",
      "expected": 1,
      "description": "simple string"
    },
    {
      "rarg": "",
      "expected": 1,
      "description": "empty string"
    },
    {
      "rarg": [1, 2, 3],
      "expected": 0,
      "description": "numeric vector"
    },
    {
      "rarg": [],
      "expected": 0,
      "description": "empty numeric vector"
    }
  ]
}
```

## Dyadic test structure

Each dyadic test case object has:
- **larg** - Left argument
- **rarg** - Right argument
- **expected** - Expected result
- **description** - Test description (string)

### Example: j.json (dyadic only)

```json
{
  "function": "j",
  "dyadic": [
    {
      "larg": " ",
      "rarg": ["abc", "def"],
      "expected": "abc def",
      "description": "join with space"
    },
    {
      "larg": "-",
      "rarg": ["abc", "def"],
      "expected": "abc-def",
      "description": "join with dash"
    },
    {
      "larg": "::",
      "rarg": ["a", "b", "c"],
      "expected": "a::b::c",
      "description": "join with multi-char separator"
    }
  ]
}
```

## Ambivalent test structure

### Example: p.json (partition - ambivalent)

```json
{
  "function": "p",
  "monadic": [
    {
      "rarg": "quick brown fox",
      "expected": ["quick", "brown", "fox"],
      "description": "default space separator"
    },
    {
      "rarg": "a::b::c",
      "expected": ["a::b::c"],
      "description": "no spaces to split on"
    }
  ],
  "dyadic": [
    {
      "larg": "/",
      "rarg": "path/to/file",
      "expected": ["path", "to", "file"],
      "description": "partition on slash"
    },
    {
      "larg": "-",
      "rarg": "a-b-c",
      "expected": ["a", "b", "c"],
      "description": "partition on dash"
    }
  ]
}
```

## JSON to APL conversion

The test runner converts JSON values to APL:
- JSON strings → APL character vectors
- JSON numbers → APL scalars/arrays
- JSON arrays → APL vectors (numeric or nested)
- Empty JSON array `[]` → APL `⍬` (zilde)

## Test runner interpretation

The test runner (`test/Run.aplf`) will:
1. Load each `.json` file from `tests/` directory using `⎕JSON`
2. Process `monadic` and/or `dyadic` test arrays
3. For each test case:
   - Convert JSON values to APL values
   - Execute function monadically or dyadically as appropriate
   - Compare result with `expected` value
4. Accumulate error counts
5. Write detailed results to `tests/test.log`
6. Print summary
