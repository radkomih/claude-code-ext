# Code Quality Plugin

A comprehensive code quality review plugin for Claude Code based on Clean Code principles, SOLID design patterns, and industry best practices.

## Overview

This plugin provides a multi-layered code review system that helps you maintain high code quality through automated analysis, focusing on:

- **Clean Code Principles**: Naming, functions, structure, and organization
- **SOLID Design Principles**: SRP, OCP, LSP, ISP, DIP
- **Security**: Vulnerability detection and best practices
- **Performance**: Algorithm efficiency and optimization opportunities
- **Testing**: Coverage and quality assessment
- **Maintainability**: Code clarity and technical debt identification

## Features

- **Slash Command**: Manual code review trigger
- **Skill**: Automatically applied when code review context is detected
- **Subagent**: Dedicated code reviewer for complex analysis with separate context
- **Comprehensive Checklist**: 360+ review criteria covering all aspects of Clean Code
- **Prioritized Feedback**: Critical, High, Medium, Low severity levels
- **Actionable Suggestions**: Before/after code examples with clear solutions

## Installation

### Option 1: From Local Directory

```bash
# Clone or copy the plugin to your plugins directory
git clone <repository-url> ~/.claude/plugins/code-quality

# Or use --plugin-dir flag
claude --plugin-dir /path/to/plugins/code-quality
```

### Option 2: Add to Project

```bash
# Create project plugins directory
mkdir -p .claude/plugins

# Copy or clone the plugin
cp -r /path/to/code-quality .claude/plugins/

# Claude Code will automatically load it
```

## Components

### 1. Slash Command

**File**: `commands/full-review.md`

Manual trigger for comprehensive code reviews.

**Usage**:
```bash
# Review entire codebase
/code-quality:full-review

# Review specific path
/code-quality:full-review src/

# Review specific file
/code-quality:full-review src/components/App.tsx
```

**When to use**: When you explicitly want a structured code review report.

---

### 2. Skill (Auto-triggered)

**File**: `skills/code-review/SKILL.md`

Automatically applied when code review context is detected.

**Triggers automatically when**:
- You mention "review", "code quality", "clean code"
- You ask to check pull requests or merge requests
- You request analysis of code changes
- You make significant code modifications

**Example phrases**:
```
"Review this code"
"Check the recent changes for issues"
"Analyze the code quality in src/"
"What issues do you see in this function?"
```

**Resources**:
- `SKILL.md`: Core review instructions and framework
- `CHECKLIST.md`: Complete 360+ criteria Clean Code checklist

---

### 3. Subagent (Delegated Review)

**File**: `agents/code-reviewer.md`

Dedicated code reviewer with separate context for complex reviews.

**Automatically suggested for**:
- Large pull requests
- Multi-file refactoring
- Complex code changes
- Deep architectural analysis

**Explicit invocation**:
```
"Use the code-reviewer subagent to analyze this PR"
"Delegate to code-reviewer for thorough analysis"
```

**Capabilities**:
- Separate context window (doesn't clutter main conversation)
- Systematic git-based change detection
- Two-pass review (architecture + implementation)
- Expert-level feedback with priorities

---

## Usage Examples

### Example 1: Quick Code Review

```bash
# Start Claude Code in your project
cd /path/to/your/project

# Ask for a review
> "Review the code in src/utils/"
```

**What happens**: The skill automatically activates and reviews the specified directory.

---

### Example 2: Pull Request Review

```bash
# Check out the PR branch
git checkout feature/new-authentication

# Ask for PR review
> "Review the changes in this PR compared to main"
```

**What happens**:
1. The skill activates
2. Claude runs `git diff main...HEAD`
3. Reviews all changed files
4. Provides prioritized feedback

---

### Example 3: Manual Comprehensive Review

```bash
# Use the slash command
> /code-quality:full-review src/
```

**What happens**: Triggers the command which provides structured review following the full checklist.

---

### Example 4: Deep Analysis with Subagent

```bash
# Request subagent review
> "Use the code-reviewer subagent to thoroughly analyze the recent refactoring"
```

**What happens**:
1. Code-reviewer subagent spawns with separate context
2. Runs `git status`, `git diff`, `git log` for context
3. Systematically reviews all changes
4. Returns organized findings to main conversation
5. You get detailed feedback without cluttering main context

---

### Example 5: Focus on Specific Issues

```bash
# Ask about specific concerns
> "Check src/api/ for security vulnerabilities"
> "Are there any performance issues in the database queries?"
> "Review error handling in the authentication module"
```

**What happens**: The skill focuses review on specific areas while still applying comprehensive criteria.

---

## Review Output Format

All components produce structured feedback organized by priority:

### Critical Issues (Must Fix Before Merge)
```
**[CRITICAL] SQL Injection Vulnerability**
- Location: `src/api/users.ts:42`
- Principle Violated: Security - Input Validation
- Issue: User input concatenated directly into SQL query
- Impact: Allows attackers to execute arbitrary SQL commands
- Solution: Use parameterized queries
- Example:
  ```typescript
  // Before (vulnerable)
  const query = `SELECT * FROM users WHERE id = ${userId}`;

  // After (secure)
  const query = 'SELECT * FROM users WHERE id = ?';
  db.execute(query, [userId]);
  ```
```

### High Priority (Should Fix)
```
**[HIGH] Function Exceeds Recommended Size**
- Location: `src/services/orderProcessor.ts:156-289`
- Principle Violated: Clean Code - Function Size (20-30 lines max)
- Issue: Function is 133 lines, handles multiple responsibilities
- Impact: Difficult to test, understand, and maintain
- Solution: Extract separate functions for each responsibility
- Example: See suggested refactoring below...
```

### Medium Priority (Consider Fixing)
```
**[MEDIUM] Code Duplication**
- Locations: `src/utils/validation.ts:23`, `src/api/auth.ts:67`
- Principle Violated: DRY Principle
- Issue: Same email validation logic duplicated
- Impact: Changes must be made in multiple places
- Solution: Extract to shared utility function
```

### Low Priority (Nice to Have)
```
**[LOW] Variable Name Could Be More Descriptive**
- Location: `src/components/Dashboard.tsx:89`
- Principle Violated: Clean Code - Intent-Revealing Names
- Issue: Variable named `d` instead of descriptive name
- Impact: Minor readability reduction
- Solution: Rename to `dashboardData` or `displayData`
```

---

## Review Criteria

The plugin reviews code against these comprehensive areas:

### 1. Naming Rules
- Intent-revealing names
- Avoid disinformation
- Pronounceable and searchable names
- Avoid noise words
- Proper abstraction level
- Consistency

### 2. Function Design
- Small functions (20-30 lines)
- Single Responsibility Principle
- Minimal arguments (0-2 ideal, max 3)
- No flag arguments
- Command-query separation
- Descriptive names
- DRY principle

### 3. Code Structure
- Small, focused classes
- High cohesion
- Single responsibility
- Proper organization and proximity
- Appropriate file sizes

### 4. Error Handling
- Exceptions over error codes
- Extracted try/catch blocks
- Single-purpose error handlers
- Informative exception context

### 5. Comments
- Prefer self-documenting code
- Remove outdated/misleading comments
- No commented-out code
- Acceptable: legal, complex algorithms, TODOs

### 6. SOLID Principles
- Single Responsibility
- Open/Closed
- Liskov Substitution
- Interface Segregation
- Dependency Inversion

### 7. Testing
- Test quality and clarity
- Good coverage
- Fast execution
- One assertion per test

### 8. Object-Oriented Design
- Objects vs data structures
- Law of Demeter
- No hybrid designs
- Clear design patterns

### 9. Concurrency
- Small synchronized sections
- Minimized shared scope
- Thread safety

### 10. Code Formatting
- Consistent formatting
- Appropriate spacing
- Proper indentation
- Reasonable file sizes

### 11. Additional Rules
- Polymorphism over switches
- DRY principle
- Structured programming
- Minimal scope
- Avoid overengineering (YAGNI)
- Performance efficiency
- Consistency

---

## Configuration

### Allowed Tools

All components have access to:
- **Read**: Read file contents
- **Grep**: Search for patterns in code
- **Glob**: Find files by pattern
- **Bash**: Execute git commands and shell operations

### Customization

You can customize the review by:

1. **Modifying the checklist** (`skills/code-review/CHECKLIST.md`)
   - Add project-specific rules
   - Adjust priorities
   - Add custom criteria

2. **Adjusting the skill** (`skills/code-review/SKILL.md`)
   - Change review approach
   - Add language-specific guidance
   - Modify output format

3. **Configuring the command** (`commands/code-review.md`)
   - Change default scope
   - Adjust instructions
   - Add custom parameters

---

## Best Practices

### For Effective Reviews

1. **Review Early and Often**
   - Run reviews on feature branches before PR
   - Review small changes incrementally
   - Don't wait until the end

2. **Focus on Changed Code**
   - Use git commands to identify changes
   - Review modified files first
   - Check related/dependent files

3. **Prioritize Issues**
   - Fix critical issues immediately (security, data loss)
   - Address high-priority items before merge
   - Track medium/low items for future improvements

4. **Use Multiple Components**
   - Quick checks: Use skill (auto-triggered)
   - Structured reviews: Use slash command
   - Deep analysis: Delegate to subagent

5. **Learn from Feedback**
   - Understand the principles behind suggestions
   - Apply patterns to future code
   - Build team coding standards

---

## Integration with Workflow

### Pre-Commit Review

```bash
# Before committing
> "Review the staged changes"

# Claude checks what's staged
git diff --staged

# Provides feedback on changes
```

### Pull Request Review

```bash
# On feature branch
> "Review this branch compared to main"

# Claude compares branches
git diff main...HEAD

# Reviews all changes
```

### Continuous Improvement

```bash
# Periodic codebase health check
> /code-quality:full-review src/

# Identifies technical debt
# Suggests refactoring opportunities
```

---

## File Structure

```
code-quality/
├── .claude-plugin/
│   └── plugin.json              # Plugin metadata
│
├── commands/
│   └── full-review.md           # Slash command: /code-quality:full-review
│
├── skills/
│   └── code-review/
│       ├── SKILL.md             # Core review skill (auto-triggered)
│       └── CHECKLIST.md         # Complete Clean Code checklist (360+ rules)
│
├── agents/
│   └── code-reviewer.md         # Dedicated reviewer subagent
│
└── README.md                    # This file
```

---

## Example Output

Here's what a typical review looks like:

```
Code Review Summary: src/api/

Reviewed Files:
- src/api/users.ts
- src/api/auth.ts
- src/api/orders.ts

=== CRITICAL ISSUES (1) ===

**[CRITICAL] SQL Injection Vulnerability**
Location: src/api/users.ts:42
Issue: Direct string concatenation in SQL query
Solution: Use parameterized queries
[Code example provided]

=== HIGH PRIORITY (3) ===

**[HIGH] Missing Error Handling**
Location: src/api/orders.ts:128
Issue: Async function lacks try/catch
Solution: Add error handling with informative messages
[Code example provided]

**[HIGH] Function Too Long**
Location: src/api/auth.ts:67-156
Issue: Function is 89 lines, multiple responsibilities
Solution: Extract to smaller functions
[Refactoring suggestion provided]

**[HIGH] Security - Exposed API Key**
Location: src/api/config.ts:12
Issue: API key hardcoded in source
Solution: Use environment variables
[Migration example provided]

=== MEDIUM PRIORITY (5) ===
[Details...]

=== LOW PRIORITY (8) ===
[Details...]

Summary:
- 1 critical security vulnerability (fix immediately)
- 3 high-priority issues (fix before merge)
- 5 medium-priority improvements
- 8 low-priority suggestions

Next Steps:
1. Fix SQL injection vulnerability in users.ts
2. Add error handling in orders.ts
3. Refactor long function in auth.ts
4. Move API key to environment variable
```

---

## Troubleshooting

### Skill Not Triggering

If the skill doesn't activate automatically:
- Use explicit language: "Review this code"
- Restart Claude Code to reload skills
- Check skill description matches your use case

### Subagent Not Available

If the subagent isn't suggested:
- Explicitly request it: "Use the code-reviewer subagent"
- Ensure the agents directory exists
- Verify the YAML frontmatter is correct

### Command Not Found

If `/code-quality:full-review` doesn't work:
- Check plugin is installed correctly
- Verify plugin.json is in .claude-plugin/
- Restart Claude Code

---

## Contributing

Contributions are welcome! To improve this plugin:

1. Add language-specific review patterns
2. Expand security vulnerability detection
3. Add performance optimization patterns
4. Improve code examples
5. Add automated testing patterns

---

## License

[Your License Here]

---

## Credits

Based on:
- **Clean Code** by Robert C. Martin
- **SOLID Principles**
- Industry best practices for secure, maintainable code

---

## Support

For issues, questions, or suggestions:
- GitHub Issues: [repository-url]
- Email: [your-email]

---

## Version History

### v1.0.0 (Current)
- Initial release
- Slash command for manual reviews
- Auto-triggering skill
- Dedicated code-reviewer subagent
- Complete Clean Code checklist (360+ criteria)
- Prioritized feedback system
- Git integration for change-focused reviews
