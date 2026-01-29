---
name: go-pre-commit
description: Pre-commit hook that checks for Go anti-patterns and Effective Go violations before committing
event: pre-commit
enabled: true
---

# Go Pre-Commit Hook

Automatically checks for Go anti-patterns and Effective Go violations before allowing commits.

## When This Hook Runs

This hook executes automatically before each `git commit` and checks staged Go files for violations.

## What It Checks

### Critical Anti-Patterns (Block Commit)

1. **gofmt Compliance**
   - All Go files must be formatted with gofmt
   - Non-negotiable formatting standard
   - Tabs for indentation required

2. **Exported Name Violations**
   - Exported identifiers must start with uppercase
   - Unexported identifiers must start with lowercase
   - No accidental exports or wrong visibility

3. **Data Race Indicators**
   - Concurrent map access without sync
   - Shared state without mutex protection
   - Send on closed channel

4. **Critical Goroutine Issues**
   - Obvious goroutine leaks (no exit condition)
   - Loop variable capture in goroutines
   - Unbounded goroutine creation

5. **Panic in Libraries**
   - Library code using panic for expected errors
   - Should return errors instead

### High Priority Anti-Patterns (Warning)

1. **Missing Error Checks**
   - Functions returning errors but not checked
   - Errors ignored with _ without comment

2. **Wrong Receiver Types**
   - Mixed pointer and value receivers on same type
   - Value receiver on method modifying state

3. **Improper Channel Usage**
   - Channels not closed when done
   - Closing from receiver side
   - Range over unclosed channel

4. **Memory Allocation Issues**
   - Using new() for slice, map, or channel
   - Not assigning append() result

## Hook Execution

```bash
# Get staged Go files
staged_files=$(git diff --cached --name-only --diff-filter=ACM | grep "\.go$" | grep -v vendor/)

if [ -z "$staged_files" ]; then
  # No Go files changed, skip check
  exit 0
fi

echo "Checking Go files for Effective Go compliance..."

# Critical Check 1: gofmt
echo "→ Checking gofmt compliance..."
unformatted=$(gofmt -l $staged_files)
if [ -n "$unformatted" ]; then
  echo "❌ CRITICAL: Files not formatted with gofmt:"
  echo "$unformatted" | sed 's/^/   /'
  echo ""
  echo "   Fix with: gofmt -w <file>"
  has_critical=true
fi

# Critical Check 2: Exported names
echo "→ Checking exported name conventions..."
for file in $staged_files; do
  # Check for exported functions/types starting with lowercase
  if grep -n "^func [a-z].*(" "$file" | head -5; then
    echo "❌ CRITICAL: Exported function with lowercase start in $file"
    has_critical=true
  fi
  if grep -n "^type [a-z].*" "$file" | head -5; then
    echo "❌ CRITICAL: Exported type with lowercase start in $file"
    has_critical=true
  fi
done

# Critical Check 3: Loop variable capture
echo "→ Checking goroutine patterns..."
for file in $staged_files; do
  # Look for common loop variable capture pattern
  if grep -B2 "go func()" "$file" | grep -q "for.*range\|for.*:="; then
    echo "⚠️  WARNING: Possible loop variable capture in goroutine: $file"
    echo "   Ensure loop variables are copied before use in goroutines"
    has_warnings=true
  fi
done

# High Priority Check: Error handling
echo "→ Checking error handling..."
for file in $staged_files; do
  # Find ignored errors
  ignored_errors=$(grep -n "_, _ =" "$file" | wc -l)
  if [ "$ignored_errors" -gt 0 ]; then
    echo "⚠️  WARNING: $ignored_errors ignored error(s) in $file"
    echo "   Errors should be checked or explicitly commented"
    has_warnings=true
  fi
done

# High Priority Check: Receiver types
echo "→ Checking receiver consistency..."
for file in $staged_files; do
  # Extract type name and check for mixed receivers
  types=$(grep "^func (" "$file" | sed 's/func (\([^ ]*\) \([^)]*\)).*/\2/' | sort -u)
  for type in $types; do
    pointer_count=$(grep "^func (\*$type)" "$file" | wc -l)
    value_count=$(grep "^func ($type)" "$file" | grep -v "\*" | wc -l)

    if [ "$pointer_count" -gt 0 ] && [ "$value_count" -gt 0 ]; then
      echo "⚠️  HIGH: Mixed pointer and value receivers on type $type in $file"
      echo "   Use consistent receiver types (all pointer or all value)"
      has_warnings=true
    fi
  done
done

# Run go vet on staged files
echo "→ Running go vet..."
if ! go vet $staged_files 2>&1 | grep -v "no Go files"; then
  vet_output=$(go vet $staged_files 2>&1)
  if echo "$vet_output" | grep -q "ERROR\|FAIL"; then
    echo "❌ CRITICAL: go vet found issues:"
    echo "$vet_output" | head -10
    has_critical=true
  fi
fi

# Summary
if [ "$has_critical" = true ]; then
  echo ""
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "❌ COMMIT BLOCKED: Critical Go anti-patterns detected"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo ""
  echo "Please fix the critical issues above before committing."
  echo ""
  echo "To learn more, run:"
  echo "  /go-review [file-name]"
  echo ""
  echo "To bypass this check (not recommended):"
  echo "  git commit --no-verify"
  echo ""
  exit 1
fi

if [ "$has_warnings" = true ]; then
  echo ""
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "⚠️  WARNING: Go anti-patterns detected"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo ""
  echo "Consider fixing these issues to improve code quality."
  echo ""
  echo "Allowing commit to proceed..."
  echo ""
fi

exit 0
```

## Claude Code Integration

Instead of using the bash script above, Claude Code will:

1. **Detect staged Go files**:
   ```bash
   git diff --cached --name-only | grep "\.go$" | grep -v vendor/
   ```

2. **Run automated checks**:
   - `gofmt -l` on staged files
   - `go vet` on staged files
   - Custom pattern detection for common issues

3. **Invoke effective-go skill**:
   - Load `effective-go-principles.json`
   - Scan staged files for anti-patterns from CHECKLIST.md
   - Check only critical and high-priority issues

4. **Report findings**:
   - **Critical**: Block commit, require fixes
   - **High**: Show warnings but allow commit
   - **Medium/Low**: Silent, can be checked with `/go-review`

5. **Provide guidance**:
   - File and line numbers
   - Which Effective Go principle is violated
   - Quick fix suggestion
   - Link to full refactoring guide

## Configuration

You can configure this hook's behavior:

### Disable Hook
```bash
# In .git/config or project settings
[effective-go]
  pre-commit = false
```

### Adjust Severity
```bash
# Only block on critical issues (default)
[effective-go]
  block-on = critical

# Block on high priority too (stricter)
[effective-go]
  block-on = high

# Never block, only warn (lenient)
[effective-go]
  block-on = none
```

### Exclude Patterns
```bash
# Skip certain files or directories
[effective-go]
  exclude = "**/*_test.go,vendor/**,generated/**"
```

### Additional Checks
```bash
# Run go test -race on pre-commit (slower)
[effective-go]
  race-check = true

# Run golangci-lint
[effective-go]
  lint = true
```

## Example Output

### Blocked Commit (Critical Issues)

```
Checking Go files for Effective Go compliance...

→ Checking gofmt compliance...
❌ CRITICAL: Files not formatted with gofmt:
   internal/service/user.go
   pkg/handler/order.go

   Fix with: gofmt -w internal/service/user.go

→ Checking exported name conventions...
❌ CRITICAL: Exported function with lowercase start in pkg/types/user.go
   Line 23: func getUserByID(id string) *User

   Fix: Rename to GetUserByID or make unexported

→ Running go vet...
❌ CRITICAL: go vet found issues:
   internal/cache/cache.go:45: concurrent map writes

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
❌ COMMIT BLOCKED: Critical Go anti-patterns detected
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Please fix the critical issues above before committing.

To learn more, run:
  /go-review internal/service/user.go

To bypass this check (not recommended):
  git commit --no-verify
```

### Allowed Commit (Warnings Only)

```
Checking Go files for Effective Go compliance...

→ Checking gofmt compliance...
✓ All files formatted

→ Checking exported name conventions...
✓ All names follow conventions

→ Checking goroutine patterns...
⚠️  WARNING: Possible loop variable capture in goroutine: internal/processor/worker.go
   Ensure loop variables are copied before use in goroutines

→ Checking error handling...
⚠️  WARNING: 2 ignored error(s) in pkg/service/order.go
   Errors should be checked or explicitly commented

→ Checking receiver consistency...
⚠️  HIGH: Mixed pointer and value receivers on type Counter in pkg/counter/counter.go
   Use consistent receiver types (all pointer or all value)

→ Running go vet...
✓ No issues found

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️  WARNING: Go anti-patterns detected
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Consider fixing these issues to improve code quality.

Allowing commit to proceed...
```

### Clean Commit

```
Checking Go files for Effective Go compliance...

→ Checking gofmt compliance...
✓ All files formatted

→ Checking exported name conventions...
✓ All names follow conventions

→ Checking goroutine patterns...
✓ No issues found

→ Checking error handling...
✓ All errors properly handled

→ Checking receiver consistency...
✓ All receivers consistent

→ Running go vet...
✓ No issues found

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Go code review passed
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

All staged Go files follow Effective Go principles.
```

## Anti-Patterns Checked

The hook checks for these patterns (from CHECKLIST.md):

### Critical (Block Commit)
- ❌ Code not formatted with gofmt
- ❌ Exported names starting with lowercase
- ❌ Unexported names starting with uppercase
- ❌ Data races (concurrent map access)
- ❌ Goroutine leaks
- ❌ Panic in library code
- ❌ Send on closed channel
- ❌ go vet failures

### High (Warn)
- ⚠️  Mixed pointer and value receivers
- ⚠️  Missing error checks
- ⚠️  Errors ignored without comment
- ⚠️  Loop variable capture in goroutines
- ⚠️  Using new() for slice/map/channel
- ⚠️  Not assigning append() result
- ⚠️  Improper channel closing

### Medium/Low (Silent, check with /go-review)
- Non-idiomatic naming
- Missing documentation
- Deep nesting
- Large interfaces
- Style issues

## Automated Checks

The hook runs these checks automatically:

```bash
# 1. Format check
gofmt -l $(git diff --cached --name-only | grep "\.go$")

# 2. Vet check
go vet $(git diff --cached --name-only | grep "\.go$")

# 3. Export naming check
grep -n "^func [a-z]" *.go
grep -n "^type [a-z]" *.go

# 4. Pattern detection
# - Loop variable capture
# - Ignored errors
# - Mixed receivers
# - Common mistakes
```

## Benefits

1. **Catch issues early**: Before code review
2. **Enforce standards**: gofmt is non-negotiable
3. **Educational**: Teaches Go best practices
4. **Fast feedback**: Immediate, at commit time
5. **Configurable**: Adjust to team needs
6. **Automated**: Runs without manual intervention

## Bypass Hook

In rare cases where you need to bypass:

```bash
# Skip all git hooks
git commit --no-verify

# Or disable just this hook
git config effective-go.pre-commit false
```

**Note**: Only bypass if you have a good reason and plan to fix issues before push/merge.

## Integration with CI/CD

This hook can be complemented by CI checks:

```yaml
# .github/workflows/go-quality.yml
name: Go Quality

on: [pull_request]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-go@v4
        with:
          go-version: '1.21'

      - name: Check gofmt
        run: |
          unformatted=$(gofmt -l .)
          if [ -n "$unformatted" ]; then
            echo "Files not formatted:"
            echo "$unformatted"
            exit 1
          fi

      - name: Run go vet
        run: go vet ./...

      - name: Run tests with race detector
        run: go test -race ./...

      - name: Run staticcheck
        run: |
          go install honnef.co/go/tools/cmd/staticcheck@latest
          staticcheck ./...
```

## Resources

- Full anti-pattern list: `skills/effective-go/CHECKLIST.md`
- Effective Go principles: `resources/effective-go-principles.json`
- Review guide: Run `/go-review [file]`
- Code analysis: Run `go-analyzer` agent
- Official guide: https://go.dev/doc/effective_go

---

This hook helps maintain Go code quality by catching anti-patterns before they enter the codebase, with special emphasis on gofmt compliance and common Go pitfalls.
