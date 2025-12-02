# Context summary for aipltest

**Quick reference document for resuming work after context compaction**

## Project overview

This is **aipltest**, a minimal testing framework for TDD in Dyalog APL version 20. The project develops:
1. A test framework (`test/Run.aplf`) that executes tests from `.apla` files
2. Utility functions (`fun/` folder) with comprehensive test coverage

Current branch: `main`
Language: Dyalog APL version 20 (Array Notation, `⎕VGET`, `⎕VSET`)

## Critical rules (from CLAUDE.md)

### Test integrity (MOST IMPORTANT)
- **NEVER modify tests to make them pass** - tests are sacred
- Tests define expected behavior; if test fails, code is wrong
- Only change tests if: provably incorrect logic, invalid assumptions, or typos
- When tests fail: (1) assume test correct, (2) investigate implementation, (3) fix implementation
- Require explicit approval with written justification for any test change

### Project workflow
- **TDD process**: Write tests FIRST → pause for human review → implement until tests pass
- **Imports**: ALL imports at file top, NO conditional imports
- **Git**: Create new branch for each development stage, frequent commits
- **No workarounds**: Fix root causes, not symptoms
- **No regressions**: All existing tests must pass
- **NEVER use `--no-verify`**: Always let pre-commit hooks run
- **MUST stop for reviews before ANY implementation**

### Git/GitHub messaging
- Terse, professional tone
- No emoji symbols
- Don't mention Claude/Anthropic/AI in commits or PRs
- Never make unproven claims about validity/effectiveness

### GitHub issue workflow
1. Pick issue, note ID
2. Create branch: `{ID}-{slug-from-issue-title}`
3. Write tests FIRST - STOP for human review
4. Commit approved tests only
5. Implement until tests pass
6. Run complete test suite
7. Create PR (squash if needed)
8. Verify CI passes
9. Await PR review
10. Merge and update epic after approval

## Project structure

```
docs/          Project plans, TODOs, reports
fun/           Dyalog APL source for utility functions (.aplf files)
test/          Test framework code (Run.aplf)
tests/         Unit test files (.apla files)
logs/          Test execution logs
```

## Key files

- `CLAUDE.md` - Critical rules and workflow (ALWAYS consult)
- `README.md` - Project description, data structures, terminology
- `docs/TODO.md` - Phase tracking (Phase 1 complete, Phase 2 current)
- `docs/test-format.md` - Test file format specification
- `test/Run.aplf` - Test runner (takes optional list of function names)

## Test file format (.apla)

Array Notation files in `tests/` containing vector of test objects:
```apl
(
 (
  description:'test description'
  expected:expectedValue
  rarg:rightArgument
  larg:leftArgument  ⍝ optional; presence indicates dyadic test
 )
 ⋯
)
```

## Utility functions (fun/)

**Completed (Phase 1)**:
- `isDict`, `isString`, `isStrings`, `isTable`, `isXref` - datatype predicates
- `isStrung` - helper checking if arg is 2-element list of vectors

**In development (Phase 2)**:
- `j` - join strings with separator
- `p` - partition string on separator
- `r` - replace characters in string

**Planned (Phases 3-4)**:
- `f` - string interpolation
- `at` - index dictionary/table/xref (Phases 5-7)

## Data structures (APL terminology)

- **String**: character vector (e.g., `'hello'`)
- **Strings**: vector of strings (e.g., `'one' 'two' 'three'`)
- **Dictionary**: pair of vectors (keys and values)
- **Object**: namespace with only variables
- **Table**: 2-list (matrix of values, column names)
- **Xref**: 3-list (matrix, column names, row names)
- **List**: 1D array of mixed types
- **Vector**: 1D array of uniform type
- **Operator**: higher-level function (not infix function)

## Recent commits

```
21e99c5 Run takes optional list of functions to test
fe6dba0 completed Phase 1: - confirmed test.Run works - completed test suite for datatype test functions - all tests passed
8293e5c first draft of tests:
```

## Git status (initial)

Untracked files:
- `test/text.apla`
- `tests/j.apla`
- `tests/p.apla`
- `tests/r.apla`

## Current phase: Phase 2

**Focus**: Simple string manipulation (j, p, r)

Tasks:
- [ ] Review existing drafts for j, p, r
- [ ] Write tests for j, p (single-char separator), r (2-char args)
- [ ] Seek approval for tests
- [ ] Implement robust versions with domain error signaling
- [ ] Run complete test suite
- [ ] Verify no regressions

## Key reminders

1. **Always read CLAUDE.md first** when resuming work
2. **Tests are sacred** - implementation must match tests
3. **Stop for review** before implementing anything
4. **All imports at top** - fail fast on missing dependencies
5. **No emoji** in any output or commits
6. **Sentence case headings** - capitalize only proper nouns/acronyms
7. Check `docs/TODO.md` for current phase status
8. Run complete test suite after any changes
9. Typography: "Dyalog APL" (proper noun)
