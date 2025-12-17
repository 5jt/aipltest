TODO
====

Task tracking for aipltest development. See [PLAN.md](../PLAN.md) for phase details.

## Phase 0: Test framework foundation

- [x] Copy TinyTest operators and function into test/
- [x] Design and document test file format
- [x] Create test/Run.aplf test runner
- [x] Write simple test file to verify framework works
- [x] Seek approval before Phase 1

## Phase 1: Datatype functions

- [x] Review existing function drafts in fun/
- [x] Write tests for isDict, isString, isStrings, isTable, isXref
- [x] Seek approval for tests
- [x] Implement/fix functions as needed
- [x] Run complete test suite
- [x] Verify no regressions
- [x] Create isStrung function
- [x] Refactor isDict, isTable, isXref to use isStrung
- [x] Enhance Run to accept optional list of function names to test

## Phase 2: Simple string manipulation

- [x] Review existing drafts for j, p, r
- [x] Write tests for j, p (single-char separator), r (2-char args)
- [x] Seek approval for tests
- [x] Implement robust versions with domain error signaling
- [x] Run complete test suite
- [x] Verify no regressions

## Phase 3: String interpolation

- [x] Review existing f draft
- [x] Write tests for f (both uses)
- [x] Seek approval for tests
- [x] Implement/fix f as needed
- [x] Run complete test suite
- [x] Verify no regressions

## Phase 4: Full string manipulation

- [x] Write tests for p and r with multi-character arguments
- [x] Seek approval for tests
- [x] Implement extended versions using ⎕R and/or ⎕S
- [x] Run complete test suite
- [x] Verify no regressions

## Phase 5: Index dictionaries

- [x] Write tests for `at` with dictionaries
- [x] Seek approval for tests
- [x] Implement `at` for dictionaries
- [x] Run complete test suite
- [x] Verify no regressions

## Phase 6: Index objects

- [x] Write tests for `at` with objects
- [x] Seek approval for tests
- [x] Extend `at` for objects
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 7: Component extraction functions

- [x] Write tests for `key` and `value`
- [x] Seek approval for tests
- [x] Implement `key` and `value`
- [x] Run complete test suite
- [x] Verify no regressions

## Phase 8: Refactor `at` for new spec (remove outrange handling)

- [ ] Revise `at` tests to expect INDEX ERROR for outrange values
- [ ] Revise `at` tests to preserve explicit defaults in dictionaries
- [ ] Seek approval for revised tests
- [ ] Refactor `at` implementation to use `key` and `value`
- [ ] Refactor `at` to signal INDEX ERROR (except dict explicit defaults)
- [ ] Run complete test suite
- [ ] Verify all tests pass

## Phase 9: Extend `at` to tables

- [ ] Write tests for `at` with tables (using new spec)
- [ ] Seek approval for tests
- [ ] Implement `at` for tables (using `key` and `value`)
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 10: Extend `at` to xrefs

- [ ] Write tests for `at` with xrefs (using new spec)
- [ ] Seek approval for tests
- [ ] Implement `at` for xrefs (using `key` and `value`)
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 11: Implement `select`

- [ ] Write tests for `select` with all data types
- [ ] Seek approval for tests
- [ ] Implement `select` (using `key` and `value`)
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 12: Implement `outr` operator

- [ ] Write tests for `outr` with all data types (both `at` and `select`)
- [ ] Seek approval for tests
- [ ] Implement `outr` operator
- [ ] Run complete test suite
- [ ] Verify no regressions

