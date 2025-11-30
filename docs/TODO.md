TODO
====

Task tracking for aipltest development. See [PLAN.md](../PLAN.md) for phase details.

## Phase 0: Test framework foundation

- [ ] Copy TinyTest operators and function into test/
- [ ] Design and document test file format
- [ ] Create test/Run.aplf test runner
- [ ] Write simple test file to verify framework works
- [ ] Seek approval before Phase 1

## Phase 1: Datatype functions

- [ ] Review existing function drafts in fun/
- [ ] Write tests for isDict, isString, isStrings, isTable, isXref
- [ ] Seek approval for tests
- [ ] Implement/fix functions as needed
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 2: Simple string manipulation

- [ ] Review existing drafts for j, p, r
- [ ] Write tests for j, p (single-char separator), r (2-char args)
- [ ] Seek approval for tests
- [ ] Implement robust versions with domain error signaling
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 3: String interpolation

- [ ] Review existing f draft
- [ ] Write tests for f (both uses)
- [ ] Seek approval for tests
- [ ] Implement/fix f as needed
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 4: Full string manipulation

- [ ] Write tests for p and r with multi-character arguments
- [ ] Seek approval for tests
- [ ] Implement extended versions using ⎕R and/or ⎕S
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 5: Index dictionaries

- [ ] Write tests for at with dictionaries
- [ ] Seek approval for tests
- [ ] Implement at for dictionaries
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 6: Index tables

- [ ] Write tests for at with tables
- [ ] Seek approval for tests
- [ ] Extend at for tables
- [ ] Run complete test suite
- [ ] Verify no regressions

## Phase 7: Index xrefs

- [ ] Write tests for at with xrefs
- [ ] Seek approval for tests
- [ ] Extend at for xrefs
- [ ] Run complete test suite
- [ ] Verify no regressions
