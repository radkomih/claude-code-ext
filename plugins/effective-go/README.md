# Effective Go Plugin

A comprehensive Claude Code plugin for analyzing and refactoring Go code using Effective Go principles and best practices from the official Go documentation.

## Overview

This plugin provides a complete Go code quality toolkit with commands, skills, agents, and hooks to help you write idiomatic Go code, identify anti-patterns, and apply best practices based on 25+ Effective Go principles.

## Features

### Core Capabilities
- **25+ Effective Go Principles**: Extracted from official Go documentation
- **Anti-Pattern Detection**: Automatically identifies Go-specific code smells
- **gofmt Enforcement**: Ensures consistent formatting across codebase
- **Error Handling Review**: Checks for proper error handling patterns
- **Concurrency Analysis**: Reviews goroutines, channels, and synchronization
- **Naming Conventions**: Enforces Go naming standards (MixedCaps, exports)
- **Receiver Type Checking**: Validates pointer vs value receiver usage
- **Interface Design**: Ensures small, focused interfaces
- **Refactoring Guidance**: Step-by-step instructions with before/after examples

### Plugin Components
- **Commands**: `/go-review` for interactive Go code review
- **Skills**: `effective-go` reusable skill for Go analysis
- **Agents**: `go-analyzer` autonomous Go expert
- **Hooks**: Pre-commit checks for Go anti-patterns
- **Resources**: Comprehensive principles JSON with decision trees

## Installation

1. Clone or place this plugin in your Claude Code plugins directory
2. Restart Claude Code or reload plugins
3. Verify installation:
   ```bash
   /go-review --help
   # or
   claude-code plugins list
   ```

## Commands

### `/go-review [path or package]`

Analyzes and reviews Go code for compliance with Effective Go principles.

**Examples:**

```bash
# Review entire Go codebase
/go-review

# Review specific package
/go-review internal/service

# Review specific file
/go-review pkg/handler/order.go

# Review with description
/go-review "check the user service for concurrency issues"
```

**What it checks:**
- gofmt compliance (critical)
- Naming conventions (exports, MixedCaps)
- Error handling patterns
- Goroutine and channel usage
- Pointer vs value receivers
- Interface design
- Memory allocation (new vs make)
- Control flow idioms
- Documentation completeness

## Skills

### `effective-go`

Reusable skill that provides comprehensive Go code analysis and refactoring capabilities. Can be invoked from commands, other skills, or used by agents.

**What it does:**
- Loads 25+ Effective Go principles from JSON
- Conducts four-phase analysis (Discovery, Strategic Planning, Tactical Implementation, Validation)
- Identifies anti-patterns using comprehensive checklist
- Provides step-by-step refactoring guidance with Go examples
- Runs automated checks (gofmt, go vet, go test -race)

**Key features:**
- Formatting and style analysis (gofmt, imports)
- Naming convention enforcement
- Error handling review
- Concurrency pattern analysis (goroutines, channels, select)
- Interface and method design assessment
- Data structure optimization

**Resources:**
- `skills/effective-go/SKILL.md` - Complete skill definition
- `skills/effective-go/CHECKLIST.md` - Anti-pattern checklist

## Agents

### `go-analyzer`

Autonomous agent that acts as a senior Go engineer. Can work independently to analyze Go code and provide comprehensive assessments.

**When to use:**
- After Go code changes
- When reviewing Go packages
- For Go code quality assessment
- To identify concurrency issues
- For performance optimization
- Before production deployment

**What it does:**
1. Loads Effective Go principles automatically
2. Runs automated checks (gofmt, go vet, race detector)
3. Maps Go code structure (packages, interfaces, methods)
4. Analyzes formatting and naming
5. Reviews error handling patterns
6. Assesses concurrency safety
7. Evaluates design patterns
8. Provides prioritized findings with examples

**How to invoke:**
```bash
# From Claude Code
Task: Use the go-analyzer agent to analyze the codebase

# Or through conversation
"Can you analyze the Go code for anti-patterns?"
```

**Output:**
- Automated check results (gofmt, vet, race)
- Anti-pattern findings with severity
- Refactoring recommendations with Go code examples
- Priority matrix for improvements
- Concrete action items

## Hooks

### Pre-Commit Hook

Automatically checks for Go anti-patterns before each commit, helping maintain code quality.

**What it checks:**

**Critical (Blocks Commit):**
- ❌ Code not formatted with gofmt
- ❌ Exported names starting with lowercase
- ❌ Data races (concurrent access)
- ❌ Goroutine leaks
- ❌ Panic in library code
- ❌ go vet failures

**High Priority (Warns):**
- ⚠️ Mixed pointer and value receivers
- ⚠️ Missing error checks
- ⚠️ Loop variable capture in goroutines
- ⚠️ Improper channel usage
- ⚠️ Using new() for slice/map/channel

**Example output:**
```
Checking Go files for Effective Go compliance...

→ Checking gofmt compliance...
❌ CRITICAL: Files not formatted with gofmt:
   internal/service/user.go

   Fix with: gofmt -w internal/service/user.go

→ Running go vet...
✓ No issues found

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
❌ COMMIT BLOCKED: 1 critical issue found
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Run: /go-review internal/service/user.go
```

**Configuration:**
```bash
# Disable hook
git config effective-go.pre-commit false

# Adjust severity
git config effective-go.block-on critical  # default
git config effective-go.block-on high      # stricter
git config effective-go.block-on none      # only warn

# Exclude patterns
git config effective-go.exclude "**/*_test.go,vendor/**"

# Enable race detection (slower)
git config effective-go.race-check true
```

**Bypass (when needed):**
```bash
git commit --no-verify
```

## Effective Go Principles Included

### Formatting
1. **gofmt** - Standard, automated formatting
2. **Semicolons** - Automatic insertion rules

### Naming
3. **Package Names** - Short, lowercase, single-word
4. **Exported Names** - Uppercase = public, lowercase = private
5. **Interface Naming** - -er suffix for single-method
6. **MixedCaps** - No underscores in identifiers

### Control Structures
7. **Guard Clauses** - Early returns, reduced nesting
8. **For Loop Patterns** - Range, traditional, infinite
9. **Switch Statements** - No fallthrough, type switches
10. **Type Switch** - Interface type handling
11. **Defer** - Cleanup and resource management

### Functions
12. **Multiple Return Values** - (result, error) pattern
13. **Named Return Values** - Documentation and defer
14. **new vs make** - Proper allocation

### Data Structures
15. **Slices** - Dynamic sequences, preferred over arrays
16. **Two-dimensional Slices** - Proper initialization
17. **Maps** - Initialization and usage
18. **Printing** - Format verbs and String() method
19. **Append** - Growing slices correctly

### Initialization
20. **Initialization** - Constants, init(), constructors
21. **Composite Literals** - Inline initialization

### Methods
22. **Pointer vs Value Receivers** - When to use each

### Interfaces
23. **Interfaces** - Implicit implementation, small interfaces
24. **Interface Conversions** - Type assertions and checks
25. **Embedding** - Composition patterns

### Concurrency
26. **Share by Communicating** - Channel-based communication
27. **Goroutines** - Lightweight concurrency
28. **Channels** - Communication pipes, buffering
29. **Channel Ranging and Closing** - Proper closing semantics
30. **Select** - Multiplexing channel operations

### Errors
31. **Error Handling** - Explicit error returns
32. **Panic** - Only for unrecoverable errors
33. **Recover** - Panic recovery in defer
34. **Error Wrapping** - Context with %w

### Web
35. **Web Servers** - HTTP server patterns

## How It Works

1. **Loads Effective Go Principles**: Reads comprehensive principle definitions from JSON
2. **Runs Automated Checks**: Executes gofmt, go vet, go test -race
3. **Analyzes Code**: Scans Go code for anti-patterns and code smells
4. **Identifies Issues**: Maps findings to specific Effective Go principles
5. **Suggests Refactorings**: Provides step-by-step guidance with Go examples
6. **Applies Changes**: Implements improvements with your approval

## Review Workflow

The plugin follows a systematic approach:

### Phase 1: Discovery
- Run automated checks (gofmt, vet, race)
- Understand Go module structure
- Map packages and interfaces
- Scan for obvious issues

### Phase 2: Strategic Planning
- Prioritize formatting fixes (gofmt first)
- Identify naming violations
- Plan error handling improvements
- Map concurrency patterns

### Phase 3: Tactical Implementation
- Apply gofmt formatting
- Fix naming conventions
- Improve error handling
- Fix receiver types
- Optimize goroutine usage
- Improve channel patterns
- Refactor interfaces

### Phase 4: Validation
- Verify gofmt compliance
- Run go vet
- Execute race detector
- Check test coverage
- Validate improvements

## Code Smell Detection

The plugin automatically identifies these anti-patterns:

**Critical:**
- Code not gofmt'd
- Wrong export visibility
- Data races
- Goroutine leaks
- Panic in libraries

**High Priority:**
- Mixed receivers
- Missing error checks
- Loop variable capture
- Improper channel usage
- Wrong allocation (new vs make)

**Medium Priority:**
- Snake_case naming
- Large interfaces
- Missing documentation
- Deep nesting
- Arrays instead of slices

**Low Priority:**
- Style inconsistencies
- Missing String() methods
- Verbose code
- Minor optimizations

## Examples

### Before: Missing Error Check

```go
// BEFORE: Silent failure ❌
func loadConfig(path string) *Config {
    data, _ := os.ReadFile(path) // Ignoring error!
    var cfg Config
    json.Unmarshal(data, &cfg) // Not checking error!
    return &cfg
}
```

### After: Proper Error Handling

```go
// AFTER: Explicit error handling ✅
func loadConfig(path string) (*Config, error) {
    data, err := os.ReadFile(path)
    if err != nil {
        return nil, fmt.Errorf("reading config from %s: %w", path, err)
    }

    var cfg Config
    if err := json.Unmarshal(data, &cfg); err != nil {
        return nil, fmt.Errorf("parsing config: %w", err)
    }

    return &cfg, nil
}
```

### Before: Loop Variable Capture

```go
// BEFORE: Race condition ❌
for _, item := range items {
    go func() {
        process(item) // BUG: captures loop variable!
    }()
}
```

### After: Proper Variable Capture

```go
// AFTER: Safe capture ✅
for _, item := range items {
    item := item // Shadow variable
    go func() {
        process(item) // Safe: uses shadowed copy
    }()
}

// OR: Pass as parameter
for _, item := range items {
    go func(it Item) {
        process(it) // Safe: parameter copy
    }(item)
}
```

### Before: Mixed Receivers

```go
// BEFORE: Inconsistent receivers ❌
type Counter struct {
    mu    sync.Mutex
    value int
}

func (c Counter) Inc() {
    c.value++ // Doesn't work - modifies copy!
}

func (c *Counter) Value() int {
    return c.value // Inconsistent with Inc()
}
```

### After: Consistent Pointer Receivers

```go
// AFTER: Consistent pointer receivers ✅
type Counter struct {
    mu    sync.Mutex
    value int
}

func (c *Counter) Inc() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.value++ // Modifies actual value
}

func (c *Counter) Value() int {
    c.mu.Lock()
    defer c.mu.Unlock()
    return c.value // Consistent receiver type
}
```

## Plugin Structure

```
plugins/effective-go/
├── .claude-plugin/
│   └── plugin.json              # Plugin metadata
├── commands/
│   └── go-review.md             # /go-review slash command
├── skills/
│   └── effective-go/
│       ├── SKILL.md             # Reusable Go analysis skill
│       └── CHECKLIST.md         # Comprehensive anti-pattern checklist
├── agents/
│   └── go-analyzer.md           # Autonomous Go expert agent
├── hooks/
│   └── pre-commit.md            # Pre-commit Go checks
├── resources/
│   └── effective-go-principles.json  # 25+ Go principles with guidance
└── README.md                    # This file
```

## Resources

- **Effective Go Principles JSON**: `resources/effective-go-principles.json`
  - Complete principle definitions from official Go docs
  - Code smells and anti-patterns for each principle
  - Step-by-step refactoring guidance
  - Decision trees for pattern selection (pointer vs value, new vs make, etc.)
  - 25+ formatting, naming, and design principles

- **Skills**: `skills/effective-go/`
  - SKILL.md - Complete Go review workflow
  - CHECKLIST.md - All anti-patterns organized by severity

- **Agents**: `agents/go-analyzer.md`
  - Autonomous Go code analysis
  - Five-phase assessment process
  - Prioritized findings with roadmap

## Automated Tools Integration

The plugin integrates with Go toolchain:

```bash
# Formatting
gofmt -l .
goimports -l .

# Analysis
go vet ./...
staticcheck ./...
golint ./...

# Testing
go test ./...
go test -race ./...
go test -cover ./...

# Complexity
gocyclo -over 15 .
```

## CI/CD Integration

Example GitHub Actions workflow:

```yaml
name: Go Quality Check

on: [push, pull_request]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-go@v4
        with:
          go-version: '1.21'

      - name: Check gofmt
        run: |
          if [ -n "$(gofmt -l .)" ]; then
            echo "Files not formatted:"
            gofmt -l .
            exit 1
          fi

      - name: Run go vet
        run: go vet ./...

      - name: Run tests with race detector
        run: go test -race -v ./...

      - name: Run staticcheck
        run: |
          go install honnef.co/go/tools/cmd/staticcheck@latest
          staticcheck ./...
```

## Decision Trees

The plugin includes decision trees for common Go questions:

### When to use pointer vs value receiver?
1. Method modifies receiver? → Pointer
2. Large struct? → Pointer
3. Type has other pointer receivers? → Pointer (consistency)
4. Contains sync.Mutex? → Pointer (must not copy)
5. Otherwise → Value

### When to return error vs panic?
1. Expected error? → Error
2. Library code? → Error
3. Programming error? → Maybe panic
4. Init/main? → Maybe panic
5. Otherwise → Error

### When to use goroutine?
1. Independent operation? → Consider goroutine
2. Can coordinate (WaitGroup/channel)? → Safe
3. Unbounded creation? → Use worker pool
4. Need result synchronously? → No goroutine

## Source Material

Principles extracted from:
- **Effective Go**: https://go.dev/doc/effective_go
- **Go Code Review Comments**: https://go.dev/wiki/CodeReviewComments
- **Go Proverbs**: https://go-proverbs.github.io/
- Official Go documentation and best practices

## Contributing

Contributions welcome! Areas for enhancement:
- Additional Go patterns
- More decision trees
- Performance optimization patterns
- Testing best practices
- Module and dependency management

## License

MIT

## Author

Radosvet M
Repository: https://github.com/radkomih/claude-code-ext

---

## Quick Start

1. Install the plugin
2. Run on your Go code:
   ```bash
   /go-review
   ```
3. Fix critical issues (gofmt first!)
4. Enable pre-commit hook for ongoing quality
5. Use go-analyzer agent for deep analysis

## Support

For issues or questions:
- Check the CHECKLIST.md for specific anti-patterns
- Review effective-go-principles.json for principle details
- Run `/go-review [file]` for targeted analysis
- Invoke go-analyzer agent for comprehensive review
