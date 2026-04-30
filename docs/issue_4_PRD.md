# Product Requirements Document: Fix @if email bug

**Issue**: #4
**Title**: Fix email ...@if... bug
**Status**: Complete
**Created**: 2026-03-09
**Last Updated**: 2026-03-09

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
- [x] Update `Contains("@if")` to `TrimStart().StartsWith("@if ")`
- [x] Update `Contains("@endif")` to `TrimStart().StartsWith("@endif")`

---

## Open Questions

_None — implementation is complete._

---

## Technical Notes (Issue-Specific)

The `@if` directive is a paragraph-level construct. Using `StartsWith` with a trailing space ensures only actual directives match, not substrings in data values.

---

## Related
- Issue #4
- PR #5
