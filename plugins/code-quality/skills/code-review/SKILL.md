---
name: code-review
description: Reviews code for quality, security, maintainability, and adherence to Clean Code principles. Use ANY time code is written, modified, or shared — including PR reviews, post-implementation checks, refactoring analysis, or whenever the user asks whether code looks right. Don't wait for an explicit review request; if code was just changed or written, this skill applies.
---

# Code Review Skill

Comprehensive code review framework based on Clean Code principles and industry best practices.

## Prerequisites

Before starting the review, read `CHECKLIST.md` (in this skill's directory).

It contains the full Clean Code rule set to apply during the review.

## Review Approach

### Two-Pass Strategy

1. **High-level architecture pass**: Overall design, structure, dependencies, modularity
2. **Low-level implementation pass**: Individual classes, functions, naming, logic line-by-line

### Core Review Areas

#### 1. Naming Quality
- Intent-revealing names that explain why, what, and how
- No misleading or ambiguous names
- Pronounceable and searchable names
- Avoid noise words and abbreviations
- Consistent naming conventions

#### 2. Function Design
- Functions are small (20-30 lines maximum)
- Single Responsibility Principle (one thing well)
- Minimal arguments (0-2 ideal, max 3)
- No flag arguments (split into separate functions)
- Command-query separation
- Descriptive function names

#### 3. Code Structure
- Small, focused classes with single responsibility
- High cohesion (related code together)
- Proper organization and proximity
- Clear separation of concerns
- Files are reasonably sized (200-500 lines)

#### 4. Error Handling
- Use exceptions, not error codes
- Extracted try/catch blocks
- Functions handling errors do nothing else
- Informative exception messages

#### 5. Comments
- Prefer self-documenting code over comments
- Remove redundant, misleading, or outdated comments
- No commented-out code (use version control)
- Acceptable: legal notices, complex algorithm explanations, TODOs

#### 6. SOLID Principles
- **Single Responsibility**: One reason to change
- **Open/Closed**: Open for extension, closed for modification
- **Liskov Substitution**: Subtypes are substitutable
- **Interface Segregation**: No forced unused interfaces
- **Dependency Inversion**: Depend on abstractions

#### 7. Testing
- Test quality matches production code quality
- Clear test structure and intent
- Good coverage of boundary conditions
- Fast execution

#### 8. Security & Performance
- No exposed secrets or credentials
- Input validation at boundaries
- No SQL injection, XSS vulnerabilities
- Efficient algorithms and data structures
- No N+1 query problems

## Output Format

Organize findings by priority:

### Critical (Must fix before merge)
- Security vulnerabilities
- Data loss risks
- Breaking changes
- Major architectural violations

### High (Should fix)
- Performance issues
- SOLID principle violations
- Missing error handling
- Insufficient test coverage

### Medium (Consider fixing)
- Code style inconsistencies
- Minor naming improvements
- Missing documentation
- Code duplication

### Low (Nice to have)
- Minor refactoring opportunities
- Additional test cases
- Code clarity enhancements

## Feedback Structure

For each issue:
1. **Location**: `file_path:line_number`
2. **Issue**: What's wrong and which principle is violated
3. **Impact**: Why it matters
4. **Solution**: How to fix it
5. **Example**: Show before/after code (when applicable)

## Additional Resources

- Use git commands to focus on recent changes: `git diff`, `git log`

## Review Process

1. **Understand scope**: Determine what code to review (recent changes vs full codebase)
2. **Examine changed files**: Use `git diff` or `git log` to identify modified files
3. **Apply checklist**: Use [CHECKLIST.md](CHECKLIST.md) for thorough analysis
4. **Document findings**: Provide specific file:line references
5. **Suggest improvements**: Include actionable recommendations with examples
6. **Prioritize**: Categorize by severity (Critical/High/Medium/Low)
7. **Verify**: Double-check for false positives before reporting

## Best Practices

- Focus on code, not the person who wrote it
- Be specific with file paths and line numbers
- Provide constructive feedback with examples
- Explain the "why" behind recommendations
- Acknowledge good practices when seen
- Suggest concrete improvements, not just problems
- Keep feedback objective and professional
