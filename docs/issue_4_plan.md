# Development Plan: Issue #4

**Issue**: #4
**Title**: Fix email ...@if... bug
**Type**: Bug Fix
**Priority**: High

## Executive Summary

Email addresses containing `@if` (e.g. `user@ifsomething.com`) in template data cause an `IndexOutOfRangeException` in `ReplaceStatements`. The `@if` directive detection uses `Contains("@if")` which falsely matches `@if` substrings in non-directive text.

---

## Problem Statement

### Current Behavior
When a template tag value contains an email address with `@if` in it (e.g. `someone@ifoo.com`), the `ReplaceStatements` method incorrectly identifies the paragraph as containing an `@if` directive. It then attempts to parse it as a conditional statement, causing an `IndexOutOfRangeException` because the text does not follow the expected `@if <tag> <operator> <value>` format.

### Expected Behavior
Email addresses and other text containing `@if` as a substring should not be treated as `@if` directives. Only paragraphs where the text starts with `@if ` (the directive syntax) should be processed.

### Impact
Application crashes when generating Word documents from templates where any tag value contains `@if` in it. This is a production-blocking bug.

---

## Technical Analysis

### Files to Modify
- `src/MiniWord/MiniWord.Implment.cs` — `ReplaceStatements` method (lines 498-526)

### Dependencies
None — self-contained fix.

### Architecture Considerations
The `@if` / `@endif` directives are paragraph-level constructs. A paragraph whose `InnerText` starts with `@if ` is a directive; all other occurrences of `@if` in text are coincidental (email addresses, URLs, etc.).

---

## Implementation Plan

### Phase 1: Fix directive detection
1. Change `Contains("@if")` to `TrimStart().StartsWith("@if ")` for the `@if` directive check
2. Change `Contains("@endif")` to `TrimStart().StartsWith("@endif")` for the `@endif` directive check

---

## Test Scenarios

### Scenario 1: Email with @if does not crash
- **Given**: A template with a tag `{{email}}`
- **When**: The tag value is `user@ifsomething.com`
- **Then**: The document is generated without error, with the email rendered as-is

### Scenario 2: Actual @if directives still work
- **Given**: A template with `@if tag == value` / `@endif` blocks
- **When**: The tag condition is evaluated
- **Then**: Conditional content is correctly included or excluded

---

## Acceptance Criteria

- [x] Email addresses containing `@if` no longer cause `IndexOutOfRangeException`
- [x] `@if` / `@endif` conditional directives continue to function correctly

---

## Build & Test Commands

```bash
# Backend
dotnet build MiniWord.sln -c Debug
dotnet test tests/MiniWordTests/MiniWordTests.csproj -c Debug
```

---

## Related Files

- `src/MiniWord/MiniWord.Implment.cs`
