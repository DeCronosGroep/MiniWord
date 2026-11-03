# Product Requirements Document: Fix @if email bug

**Issue**: #4
**Title**: Fix email ...@if... bug
**Status**: Complete
**Created**: 2026-03-09
**Last Updated**: 2026-10-27

---

## Overview

Fix a crash when generating Word documents from templates where tag values contain email addresses with `@if` as a substring.

---

## Goals & Objectives

### Primary Goals
- Prevent `IndexOutOfRangeException` when email addresses contain `@if`
- Maintain correct behavior of `@if`/`@endif` conditional directives

### Success Metrics
- No crash with email addresses like `user@ifsomething.com`
- Existing conditional directive tests pass

---

## Functional Requirements

### Must Have (P0)
- [x] **FR-1**: `ReplaceStatements` must not treat `@if` substrings in email addresses as directives
- [x] **FR-2**: `@if`/`@endif` conditional blocks must continue to work correctly

---

## Timeline & Milestones

### Milestone 1: Fix directive detection
- [x] `@if`: only a paragraph shaped `@if tag` or `@if tag operator value` with a known tag is a directive (`IsIfStatement`)
- [x] `@endif`: matches `@endif` not followed by a letter or digit (`IsEndIfStatement`), so `jan@endifco.be` does not close a block
- [x] Every template that works in 0.7.1 gives the same output (review feedback: `@endif.`, `End @endif`, `(@endif)`, `@if: …`, `x@if …`)

### Milestone 2: Regression tests and release (review feedback, PR #5)
- [x] Add regression tests for `@if` emails in foreach rows and for directive evaluation
- [x] Bump package version to `0.7.2`

---

## Open Questions

_None — implementation is complete._

---

## Technical Notes (Issue-Specific)

The `@if` directive is a paragraph-level construct. Matching on the statement shape (2 or 4 words, known tag) instead of a substring ensures only actual directives match, not data values like email addresses, while every directive that worked in 0.7.1 still matches. A prefix match (`StartsWith`) was tried and dropped: it breaks templates like `End @endif` and `@if: Points > 100` that work in 0.7.1.

---

## Related
- Issue #4
- PR #5
