---
name: ddd-pre-commit
description: Pre-commit hook that checks for DDD anti-patterns before committing
event: pre-commit
enabled: true
---

# DDD Pre-Commit Hook

Automatically checks for Domain-Driven Design anti-patterns before allowing commits.

## When This Hook Runs

This hook executes automatically before each `git commit` and checks staged files for DDD violations.

## What It Checks

### Critical Anti-Patterns (Block Commit)

1. **Anemic Domain Models**
   - Entities with only getters/setters
   - Business logic in services instead of domain objects

2. **Domain Logic in Wrong Layers**
   - Business rules in controllers/UI
   - Domain logic in infrastructure layer

3. **Broken Invariants**
   - Public setters on entities allowing invalid state
   - No validation in constructors

4. **Missing Aggregate Boundaries**
   - Direct updates to nested entities
   - No aggregate root controlling access

### High Priority Anti-Patterns (Warning)

1. **Primitive Obsession**
   - Using primitives instead of value objects
   - No domain-specific types

2. **Missing Domain Events**
   - State changes without event publication
   - No event sourcing for audit trail

3. **Repository Anti-Patterns**
   - Repositories for non-aggregate roots
   - Domain logic in repository methods

4. **Mutable Value Objects**
   - Value objects with setters
   - No immutability guarantees

## Hook Execution

```bash
# Get staged files
staged_files=$(git diff --cached --name-only --diff-filter=ACM)

# Filter for domain-related files
domain_files=$(echo "$staged_files" | grep -E "(domain|entity|aggregate|repository|service)" || true)

if [ -z "$domain_files" ]; then
  # No domain files changed, skip check
  exit 0
fi

# Load DDD principles
principles=$(cat plugins/ddd-refactor/resources/ddd-principles.json)

# Check each domain file for anti-patterns
for file in $domain_files; do
  # Scan for critical anti-patterns

  # 1. Check for anemic models (entities with only getters/setters)
  if grep -q "class.*Entity" "$file"; then
    # Count getters/setters
    getters=$(grep -c "get[A-Z]" "$file" || true)
    setters=$(grep -c "set[A-Z]" "$file" || true)
    methods=$(grep -c "^[[:space:]]*[a-z].*(" "$file" || true)

    # If mostly getters/setters, likely anemic
    if [ $((getters + setters)) -gt $((methods / 2)) ]; then
      echo "❌ CRITICAL: Anemic domain model detected in $file"
      echo "   Entities should contain business logic, not just getters/setters"
      echo "   See: plugins/ddd-refactor/resources/ddd-principles.json - 'Entity' pattern"
      has_critical=true
    fi
  fi

  # 2. Check for domain logic in controllers
  if echo "$file" | grep -qE "(controller|handler|route)"; then
    if grep -qE "(calculate|validate|process|compute)" "$file"; then
      echo "❌ CRITICAL: Business logic detected in controller: $file"
      echo "   Domain logic should be in domain layer, not controllers"
      echo "   See: Layered Architecture pattern"
      has_critical=true
    fi
  fi

  # 3. Check for public setters in entities
  if grep -q "class.*Entity" "$file"; then
    if grep -q "public.*set[A-Z]" "$file"; then
      echo "❌ CRITICAL: Public setters in entity: $file"
      echo "   Entities should enforce invariants, not expose public setters"
      echo "   Use methods with domain meaning instead"
      has_critical=true
    fi
  fi

  # 4. Check for mutable value objects
  if grep -qE "(ValueObject|value-object)" "$file"; then
    if grep -qE "(set[A-Z]|public [a-z].*=)" "$file"; then
      echo "⚠️  HIGH: Mutable value object detected in $file"
      echo "   Value objects should be immutable"
      echo "   Consider using readonly/final/const"
      has_warnings=true
    fi
  fi

  # 5. Check for repositories of non-roots
  if grep -q "Repository" "$file"; then
    # Check if it's for an entity that's likely not a root
    if grep -qE "(LineRepository|ItemRepository|DetailRepository)" "$file"; then
      echo "⚠️  HIGH: Repository for likely non-aggregate root in $file"
      echo "   Repositories should only exist for aggregate roots"
      echo "   Access nested entities through the root"
      has_warnings=true
    fi
  fi
done

# Summary
if [ "$has_critical" = true ]; then
  echo ""
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "❌ COMMIT BLOCKED: Critical DDD anti-patterns detected"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo ""
  echo "Please fix the critical issues above before committing."
  echo ""
  echo "To learn more about these patterns, run:"
  echo "  /refactor [file-name]"
  echo ""
  echo "Or to bypass this check (not recommended):"
  echo "  git commit --no-verify"
  echo ""
  exit 1
fi

if [ "$has_warnings" = true ]; then
  echo ""
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo "⚠️  WARNING: DDD anti-patterns detected"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
  echo ""
  echo "Consider fixing these issues to improve domain model quality."
  echo ""
  echo "Allowing commit to proceed..."
  echo ""
fi

exit 0
```

## Claude Code Integration

Instead of using the bash script above, Claude Code will:

1. **Detect staged domain files**:
   ```bash
   git diff --cached --name-only | grep -E "(domain|entity|aggregate|repository)"
   ```

2. **Invoke ddd-refactor skill**:
   - Load `ddd-principles.json`
   - Scan staged files for anti-patterns from CHECKLIST.md
   - Check only critical and high-priority issues

3. **Report findings**:
   - **Critical**: Block commit, require fixes
   - **High**: Show warnings but allow commit
   - **Medium/Low**: Silent, can be checked with `/refactor`

4. **Provide guidance**:
   - File and line numbers
   - Which DDD principle is violated
   - Quick fix suggestion
   - Link to full refactoring guide

## Configuration

You can configure this hook's behavior:

### Disable Hook
```bash
# In .git/config or project settings
[ddd-refactor]
  pre-commit = false
```

### Adjust Severity
```bash
# Only block on critical issues (default)
[ddd-refactor]
  block-on = critical

# Block on high priority too (stricter)
[ddd-refactor]
  block-on = high

# Never block, only warn (lenient)
[ddd-refactor]
  block-on = none
```

### Exclude Patterns
```bash
# Skip certain files or directories
[ddd-refactor]
  exclude = "test/**,**/migrations/**,**/generated/**"
```

## Example Output

### Blocked Commit (Critical Issues)

```
Checking staged files for DDD anti-patterns...

❌ CRITICAL: Anemic domain model detected
   File: src/domain/Order.ts:15-45
   Issue: Order entity has only getters/setters, no business logic
   Principle: Entity Pattern (Tactical)

   Quick Fix:
   - Move validation from OrderService to Order entity
   - Add methods: order.cancel(), order.ship()
   - Enforce invariants in entity

   Run for details: /refactor src/domain/Order.ts

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
❌ COMMIT BLOCKED: 1 critical DDD issue found
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Fix the issues above or use --no-verify to bypass (not recommended).
```

### Allowed Commit (Warnings Only)

```
Checking staged files for DDD anti-patterns...

⚠️  HIGH: Primitive obsession detected
   File: src/domain/Customer.ts:23
   Issue: Using string for EmailAddress
   Suggestion: Create EmailAddress value object

   Run for details: /refactor src/domain/Customer.ts

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Commit allowed (1 warning)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Consider addressing warnings to improve domain model quality.
```

### Clean Commit

```
Checking staged files for DDD anti-patterns...

✅ No DDD anti-patterns detected
   Staged domain files follow DDD principles

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Commit approved
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Anti-Patterns Checked

The hook checks for these patterns (from CHECKLIST.md):

### Critical (Block Commit)
- ❌ Anemic domain models
- ❌ Domain logic in controllers/UI
- ❌ Public setters breaking invariants
- ❌ Missing aggregate boundaries
- ❌ Domain logic in infrastructure

### High (Warn)
- ⚠️  Primitive obsession
- ⚠️  Mutable value objects
- ⚠️  Repositories for non-roots
- ⚠️  Missing domain events
- ⚠️  Entities without clear identity
- ⚠️  Large aggregates

### Medium/Low (Silent, check with /refactor)
- Naming inconsistencies
- Missing factory methods
- Service bloat
- Missing specifications

## Benefits

1. **Catch issues early**: Before they reach code review
2. **Educational**: Teaches DDD principles in context
3. **Consistent quality**: Enforces standards automatically
4. **Fast feedback**: Immediate, at commit time
5. **Configurable**: Adjust to team's needs

## Bypass Hook

In rare cases where you need to bypass:

```bash
# Skip all git hooks
git commit --no-verify

# Or disable just this hook
git config ddd-refactor.pre-commit false
```

**Note**: Only bypass if you have a good reason and plan to fix issues later.

## Resources

- Full anti-pattern list: `skills/ddd-refactor/CHECKLIST.md`
- DDD principles: `resources/ddd-principles.json`
- Refactoring guide: Run `/refactor [file]`
- Domain analysis: Run `ddd-analyzer` agent

---

This hook helps maintain domain model quality by catching anti-patterns before they enter the codebase.
