---
description: Comprehensive code review following Clean Code principles
allowed-tools: Read, Grep, Glob, Bash
argument-hint: [path]
---

# Code Review - Clean Code Principles

Perform a comprehensive code review based on Clean Code principles and industry best practices.

## Scope

Review: ${ARGUMENTS:-"the entire codebase"}

## Review Strategy

Use the **code-review skill** to conduct a thorough analysis with:

1. **Two-pass approach**:
   - High-level architecture pass (design, structure, dependencies)
   - Low-level implementation pass (functions, naming, logic)

2. **Focus areas**:
   - Naming quality and clarity
   - Function design and size
   - Code structure and organization
   - Error handling
   - SOLID principles adherence
   - Testing quality
   - Security vulnerabilities
   - Performance issues

## Instructions

1. **Identify scope**: Determine what to review (recent changes vs full codebase)
2. **Use git for context**: Run `git diff` or `git log` to see recent changes if applicable
3. **Apply the skill**: Use the code-review skill which contains detailed checklists
4. **Provide specific feedback**:
   - File paths and line numbers (e.g., `src/app.js:42`)
   - What's wrong and which principle is violated
   - Why it matters
   - How to fix it with examples
5. **Prioritize findings**:
   - **Critical**: Security, data loss, breaking changes
   - **High**: Performance, SOLID violations, missing error handling
   - **Medium**: Code style, naming, duplication
   - **Low**: Minor refactoring opportunities

## Output Format

Organize findings by priority with clear, actionable feedback.

For each issue include:
- Location (file:line)
- Issue description
- Principle violated
- Concrete solution with code example

Begin the review now.
