---
description: Review Go code for Effective Go compliance and best practices
allowed-tools: Read, Grep, Glob, Bash, Edit, Write
argument-hint: [path or package]
---

# Go Code Review Assistant

Analyzes Go code for compliance with Effective Go principles and Go best practices.

## Scope

Target: ${ARGUMENTS:-"the entire Go codebase"}

## Strategy

Use the **effective-go skill** to conduct thorough Go code analysis with:

1. **Four-phase approach**:
   - Discovery & Analysis (understand codebase, identify anti-patterns)
   - Strategic Planning (formatting, naming, architecture)
   - Tactical Implementation (error handling, concurrency, interfaces)
   - Validation & Testing (go vet, race detection, testing)

2. **Focus areas**:
   - gofmt compliance
   - Naming conventions (MixedCaps, exported/unexported)
   - Error handling patterns
   - Goroutine and channel usage
   - Pointer vs value receivers
   - Interface design
   - Concurrency patterns
   - Memory allocation (new vs make)
   - Defer usage
   - Control flow idioms

## Instructions

1. **Identify scope**: Determine what to review (package, module, or full codebase)
2. **Use git for context**: Run `git log` to understand recent changes if applicable
3. **Apply the skill**: Use the effective-go skill which contains:
   - Complete Effective Go principles from `effective-go-principles.json`
   - Anti-pattern detection checklist
   - Refactoring guidance with Go-specific examples
4. **Provide specific feedback**:
   - File paths and line numbers (e.g., `internal/service/order.go:45`)
   - What anti-pattern exists and which Effective Go principle applies
   - Why it matters for Go code quality
   - How to fix it with before/after code examples
5. **Prioritize findings**:
   - **Critical**: No gofmt, wrong exports, data races, goroutine leaks
   - **High**: Wrong receivers, missing error checks, channel issues
   - **Medium**: Naming issues, missing docs, non-idiomatic patterns
   - **Low**: Style improvements, minor optimizations

## Output Format

Organize findings by priority with clear, actionable refactoring steps.

For each issue include:
- Location (file:line)
- Anti-pattern description
- Effective Go principle to apply
- Step-by-step refactoring guidance
- Concrete Go code example showing before/after

## Begin Review

The effective-go skill will guide you through the complete review process:

1. Load Effective Go principles from `resources/effective-go-principles.json`
2. Run automated checks (gofmt, go vet, golint)
3. Analyze the specified target for anti-patterns
4. Identify code smells using the comprehensive checklist
5. Propose refactorings with before/after examples
6. Apply changes with your approval

Start now by invoking the effective-go skill to analyze the specified target.
