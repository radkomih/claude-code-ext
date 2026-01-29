# Effective Go Anti-Pattern Checklist

Comprehensive checklist for identifying Go-specific anti-patterns and violations of Effective Go principles.

## Critical Anti-Patterns (Block Commit)

### Formatting
- [ ] Code not formatted with gofmt (run `gofmt -l .` to check)
- [ ] Using spaces instead of tabs for indentation
- [ ] Opening braces on separate lines (causes semicolon insertion issues)
- [ ] Manual semicolons at end of lines (except in for loops)

### Naming - Exported Identifiers
- [ ] Exported function/type/variable starting with lowercase letter
- [ ] Unexported names starting with uppercase (accidental export)
- [ ] Package names with underscores (e.g., `user_service`)
- [ ] Package names with MixedCaps (e.g., `UserService`)

### Concurrency - Data Races
- [ ] Concurrent map reads and writes without synchronization
- [ ] Shared variables accessed from multiple goroutines without mutex
- [ ] Data races detected by `go test -race`
- [ ] Sending on closed channel (causes panic)
- [ ] Closing channel from receiver side

### Error Handling - Critical Failures
- [ ] Panic used in library/package code for expected errors
- [ ] Errors completely ignored without `_` (function not assigned)
- [ ] nil pointer dereference from unchecked error

### Goroutines - Leaks
- [ ] Goroutines with no termination condition (infinite loops with no exit)
- [ ] Channels that block forever (no sender/receiver)
- [ ] Goroutines not coordinated (no WaitGroup, no done channel)

## High Priority Anti-Patterns (Fix Before Deployment)

### Receiver Types
- [ ] Mixing pointer and value receivers on same type
- [ ] Value receiver on method that appears to modify state
- [ ] Value receiver on large struct (expensive copy)
- [ ] Value receiver on type containing sync.Mutex or sync.WaitGroup
- [ ] Pointer receiver on small, simple value type unnecessarily

### Error Handling
- [ ] Errors ignored with `_` without comment explaining why
- [ ] Error returned without adding context
- [ ] Using `%v` instead of `%w` for error wrapping (loses error chain)
- [ ] Comparing errors with `==` instead of `errors.Is`
- [ ] Type asserting errors instead of using `errors.As`
- [ ] Missing error check on deferred Close() call

### Goroutines and Channels
- [ ] Loop variable captured in goroutine without copying
```go
// Bad
for i := range items {
    go func() {
        process(items[i]) // BUG: i changes!
    }()
}
```
- [ ] Creating unbounded number of goroutines (should use worker pool)
- [ ] Not closing channels when done (causes goroutine leaks)
- [ ] Closing channel that's already closed (panic)
- [ ] Range over channel that's never closed (deadlock)

### Memory Allocation
- [ ] Using `new()` for slices, maps, or channels (use `make()`)
- [ ] Not initializing maps before use (nil map panic on write)
- [ ] Not assigning result of `append()` back to variable
- [ ] Missing capacity hint in `make()` when size is known

### Interfaces
- [ ] Large interfaces with many methods (>5 for application code)
- [ ] Interface with "Interface" suffix (e.g., `ReaderInterface`)
- [ ] Interfaces defined in provider package instead of consumer
- [ ] Using empty interface `interface{}` when specific type is known

## Medium Priority Anti-Patterns (Fix in Next Sprint)

### Naming Conventions
- [ ] Snake_case in identifiers (use MixedCaps or mixedCaps)
- [ ] Inconsistent acronym casing (use `HTTPServer` not `HttpServer`)
- [ ] Single-method interface without -er suffix
- [ ] Generic names like `Manager`, `Helper`, `Util` for packages
- [ ] Getter methods prefixed with `Get` (just use property name)
- [ ] Setter methods when direct field access or methods would be clearer

### Documentation
- [ ] Missing doc comments on exported functions/types
- [ ] Doc comments not starting with the name being documented
- [ ] Incomplete sentences in doc comments
- [ ] Blank line between doc comment and declaration

### Control Structures
- [ ] Deep nesting instead of guard clauses (early returns)
- [ ] Using traditional for loop when range would be clearer
- [ ] Long if-else chains instead of switch statement
- [ ] Not using type switch for interface type checking
- [ ] Break statements in switch (unnecessary, no fallthrough by default)

### Functions
- [ ] Naked returns in functions longer than 5-10 lines
- [ ] Named return parameters without using naked return
- [ ] More than 3 return values (consider struct)
- [ ] Boolean return instead of error for failure cases

### Defer
- [ ] Defer used in loop (can cause memory buildup)
- [ ] Cleanup done manually before return instead of defer
- [ ] Not using defer for mutex unlock
- [ ] Not using defer for file close

### Data Structures
- [ ] Using array when slice would be more appropriate
- [ ] Not using composite literals for initialization
- [ ] Two-dimensional slices not properly allocated
- [ ] Modifying slice while ranging over it

### Maps
- [ ] Not checking key existence with comma-ok idiom
- [ ] Writing to nil map (panic)
- [ ] Assuming iteration order (maps are random)
- [ ] Not pre-allocating map with size hint when known

### Error Handling
- [ ] Creating errors with `errors.New()` when formatting needed
- [ ] Not using custom error types when needed
- [ ] Returning error and successful value (return zero value with error)

## Low Priority Anti-Patterns (Technical Debt)

### Style and Idioms
- [ ] Variable names too short in large scope (or too long in small scope)
- [ ] Not using blank identifier for intentionally unused values
- [ ] Explicit comparison to bool (`if x == true` vs `if x`)
- [ ] Comparing to nil when unnecessary

### Initialization
- [ ] Complex logic in init() functions
- [ ] Side effects in init() (hard to test)
- [ ] Not providing constructor functions for types needing validation
- [ ] Using new() when composite literal is clearer

### Printing and Formatting
- [ ] Not implementing String() method for custom types
- [ ] Using wrong format verbs (%v vs %d vs %s)
- [ ] String concatenation instead of fmt.Sprintf

### Embedding
- [ ] Deep embedding hierarchies
- [ ] Embedding when explicit field would be clearer
- [ ] Name collision from embedding

### Context
- [ ] Not using context for cancellation/timeout
- [ ] Storing context in struct (should be function parameter)
- [ ] Creating context but not canceling it (leak)

## Quick Reference by File Type

### Main Package (`main.go`)
```
❌ Bad: Business logic in main
✅ Good: Thin main, delegates to packages
```

### Package Files
```
❌ Bad: Missing package comment
✅ Good: Package comment explaining purpose
```

### Interface Definitions
```
❌ Bad: Large interface (10+ methods)
✅ Good: Small, focused interfaces (1-3 methods)
```

### Struct Methods
```
❌ Bad: Mixed pointer and value receivers
✅ Good: Consistent receiver types
```

### Error Handling
```
❌ Bad: _, _ = function() // Ignoring errors
✅ Good: if err != nil { return fmt.Errorf("context: %w", err) }
```

## Severity Levels

### Critical (Block commit/merge)
- Code not gofmt'd
- Exported names starting with lowercase
- Data races (concurrent access without sync)
- Panic in library code
- Goroutine leaks
- Sending on closed channel

### High (Fix before deployment)
- Wrong receiver types (mixed or inappropriate)
- Missing error checks
- Loop variable capture in goroutine
- Improper channel usage
- Using new() for slice/map/channel
- Missing append() assignment

### Medium (Fix in next sprint)
- Non-idiomatic naming (snake_case)
- Missing documentation on exported identifiers
- Deep nesting instead of guard clauses
- Large interfaces
- Not using defer for cleanup
- Arrays when slices would work better

### Low (Technical debt)
- Minor style inconsistencies
- Missing String() methods
- Overly verbose code
- Could use better variable names

## Automated Checks

### Tools to Run

```bash
# Format check
gofmt -l . | grep -v vendor/

# Vet (built-in analysis)
go vet ./...

# Race detector
go test -race ./...

# Static analysis
staticcheck ./...
# or
golangci-lint run

# Linter
golint ./...

# Imports
goimports -l .

# Ineffective assignments
ineffassign ./...

# Cyclomatic complexity
gocyclo -over 15 .

# Go report card
goreportcard-cli -v
```

### Pre-commit Hook Checks

The pre-commit hook should check for:

1. **Critical** (block commit):
   - gofmt compliance
   - Exported names starting with lowercase
   - Obvious goroutine leaks
   - Missing error checks on critical operations

2. **High** (warn but allow):
   - Mixed receivers
   - Ignored errors with _
   - Loop variable capture

3. **Medium/Low** (silent, check with `/go-review`):
   - Style issues
   - Documentation gaps
   - Minor idiom improvements

## Decision Trees

### Should I use a pointer receiver?
1. Does method modify the receiver? → YES: Use pointer
2. Is receiver a large struct (>few words)? → YES: Use pointer
3. Does type have other pointer receiver methods? → YES: Use pointer (consistency)
4. Does receiver contain mutex/sync types? → YES: Use pointer (must not copy)
5. Otherwise → Use value receiver

### Should I return an error?
1. Can the function fail for expected reasons? → YES: Return error
2. Is this library/package code? → YES: Return error (don't panic)
3. Is this a programming error (bug)? → MAYBE: Consider panic
4. Is this initialization code (init, main)? → MAYBE: OK to panic
5. Otherwise → Return error

### Should I use a goroutine?
1. Is the operation independent? → YES: Consider goroutine
2. Can I coordinate completion (WaitGroup/channel)? → YES: Safe to use
3. Will this create unbounded goroutines? → NO: Use worker pool instead
4. Do I need the result synchronously? → NO: Don't use goroutine
5. Can the goroutine leak? → FIX: Add termination condition

### Should I use a channel?
1. Communicating between goroutines? → YES: Use channel
2. Need synchronization? → YES: Unbuffered channel
3. Need buffering? → YES: Buffered channel with size
4. Simple mutex would work? → MAYBE: Mutex might be clearer
5. Only one goroutine? → NO: Don't need channel

## Common Violations by Pattern

### Error Handling
```go
❌ Bad
result, _ := doSomething() // Ignoring error
doSomethingElse() // Not checking error

✅ Good
result, err := doSomething()
if err != nil {
    return fmt.Errorf("doing something: %w", err)
}

if err := doSomethingElse(); err != nil {
    return fmt.Errorf("doing something else: %w", err)
}
```

### Receiver Types
```go
❌ Bad - Mixed receivers
type Counter struct { count int }
func (c Counter) Inc() { c.count++ } // Value - doesn't work!
func (c *Counter) Value() int { return c.count } // Pointer - inconsistent

✅ Good - Consistent pointer receivers
type Counter struct { count int }
func (c *Counter) Inc() { c.count++ }
func (c *Counter) Value() int { return c.count }
```

### Goroutine Coordination
```go
❌ Bad - No coordination
for _, item := range items {
    go process(item) // When do they finish? How to get errors?
}

✅ Good - Proper coordination
var wg sync.WaitGroup
for _, item := range items {
    wg.Add(1)
    item := item // Capture
    go func() {
        defer wg.Done()
        process(item)
    }()
}
wg.Wait()
```

## Refactoring Priority Matrix

| Impact | Effort Low | Effort Medium | Effort High |
|--------|------------|---------------|-------------|
| **Critical** | DO NOW | DO NOW | PLAN & DO |
| **High** | DO NOW | DO SOON | PLAN |
| **Medium** | DO SOON | SCHEDULE | CONSIDER |
| **Low** | BACKLOG | BACKLOG | SKIP |

## Usage

Use this checklist during:
- Code reviews
- Pre-commit checks
- Refactoring sessions
- Go code audits
- Learning Go best practices
- Onboarding new Go developers

For each identified issue, reference the specific principle from `effective-go-principles.json` for detailed refactoring guidance.
