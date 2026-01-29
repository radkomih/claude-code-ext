# DDD Refactor Plugin

A comprehensive Claude Code plugin for refactoring codebases using Domain-Driven Design (DDD) principles from Eric Evans' seminal book.

## Overview

This plugin provides a complete DDD toolkit with commands, skills, agents, and hooks to help you apply tactical and strategic DDD patterns, identify code smells, and build better domain models based on 19+ core DDD principles.

## Features

### Core Capabilities
- **19+ DDD Principles**: Extracted directly from the DDD book
- **Code Smell Detection**: Automatically identifies anti-patterns
- **Strategic Design**: Bounded contexts, context mapping, ubiquitous language
- **Tactical Patterns**: Entities, value objects, aggregates, repositories, domain events
- **Refactoring Guidance**: Step-by-step instructions with before/after examples
- **Architecture Patterns**: Layered architecture, anti-corruption layers

### Plugin Components
- **Commands**: `/refactor` for interactive refactoring
- **Skills**: `ddd-refactor` reusable skill for domain analysis
- **Agents**: `ddd-analyzer` autonomous domain expert
- **Hooks**: Pre-commit checks for DDD anti-patterns
- **Resources**: Comprehensive principles JSON and checklists

## Installation

1. Clone or place this plugin in your Claude Code plugins directory
2. Restart Claude Code or reload plugins
3. Verify installation:
   ```bash
   /refactor --help
   # or
   claude-code plugins list
   ```

## Commands

### `/refactor [path]`

Analyzes and refactors code using DDD principles.

**Examples:**

```bash
# Refactor entire codebase
/refactor

# Refactor specific directory
/refactor src/domain

# Refactor specific file
/refactor src/domain/Order.java

# Refactor with description
/refactor "improve the order processing domain model"
```

## Skills

### `ddd-refactor`

Reusable skill that provides comprehensive DDD analysis and refactoring capabilities. Can be invoked from commands, other skills, or used by agents.

**What it does:**
- Loads 19+ DDD principles from JSON
- Conducts four-phase analysis (Discovery, Strategic Planning, Tactical Implementation, Validation)
- Identifies anti-patterns using comprehensive checklist
- Provides step-by-step refactoring guidance with examples
- Supports all major programming languages

**Key features:**
- Strategic design analysis (bounded contexts, ubiquitous language)
- Tactical pattern application (entities, value objects, aggregates)
- Architecture assessment (layering, integration)
- Language-specific examples

**Resources:**
- `skills/ddd-refactor/SKILL.md` - Complete skill definition
- `skills/ddd-refactor/CHECKLIST.md` - Anti-pattern checklist

## Agents

### `ddd-analyzer`

Autonomous agent that acts as a senior DDD architect. Can work independently to analyze domain models and provide comprehensive assessments.

**When to use:**
- After domain model changes
- When refactoring business logic
- For domain architecture assessment
- To identify strategic design issues
- For comprehensive domain analysis

**What it does:**
1. Loads DDD principles automatically
2. Maps domain structure (entities, aggregates, repositories)
3. Analyzes strategic design (contexts, language, integration)
4. Evaluates tactical patterns (entities, value objects, events)
5. Assesses architecture (layering, boundaries)
6. Provides prioritized findings with refactoring roadmap

**How to invoke:**
```bash
# From Claude Code
Task: Use the ddd-analyzer agent to analyze the domain model in src/domain

# Or through conversation
"Can you analyze the domain model for DDD anti-patterns?"
```

**Output:**
- Bounded context analysis
- Ubiquitous language assessment
- Anti-pattern findings with severity
- Refactoring recommendations with examples
- Priority matrix for improvements

## Hooks

### Pre-Commit Hook

Automatically checks for DDD anti-patterns before each commit, helping maintain domain model quality.

**What it checks:**

**Critical (Blocks Commit):**
- ❌ Anemic domain models
- ❌ Domain logic in controllers/UI
- ❌ Public setters breaking invariants
- ❌ Missing aggregate boundaries

**High Priority (Warns):**
- ⚠️ Primitive obsession
- ⚠️ Mutable value objects
- ⚠️ Repositories for non-aggregate roots
- ⚠️ Missing domain events

**Example output:**
```
Checking staged files for DDD anti-patterns...

❌ CRITICAL: Anemic domain model detected
   File: src/domain/Order.ts:15-45
   Issue: Order entity has only getters/setters
   Principle: Entity Pattern

   Quick Fix: Move validation to Order entity
   Run: /refactor src/domain/Order.ts

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
❌ COMMIT BLOCKED: 1 critical issue found
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Configuration:**
```bash
# Disable hook
git config ddd-refactor.pre-commit false

# Adjust severity
git config ddd-refactor.block-on critical  # default
git config ddd-refactor.block-on high      # stricter
git config ddd-refactor.block-on none      # only warn

# Exclude patterns
git config ddd-refactor.exclude "test/**,**/generated/**"
```

**Bypass (when needed):**
```bash
git commit --no-verify
```

## DDD Principles Included

### Strategic Design
1. **Ubiquitous Language** - Shared vocabulary between domain experts and developers
2. **Bounded Context** - Explicit boundaries where a model applies
3. **Context Map** - Relationships between bounded contexts
4. **Continuous Integration** - Preventing model fragmentation

### Building Blocks (Tactical Patterns)
5. **Entity** - Objects defined by identity
6. **Value Object** - Immutable objects defined by attributes
7. **Aggregate** - Consistency boundaries for groups of objects
8. **Repository** - Collection-like access to aggregate roots
9. **Factory** - Complex object creation
10. **Domain Service** - Stateless domain operations
11. **Module** - Organizing related concepts
12. **Domain Events** - Recording significant domain occurrences

### Architecture
13. **Layered Architecture** - Separation of concerns
14. **Model-Driven Design** - Code as expression of the model

### Context Mapping Patterns
15. **Anti-Corruption Layer** - Isolating from external systems
16. **Shared Kernel** - Explicitly shared model subset
17. **Customer-Supplier** - Formalized upstream-downstream relationship
18. **Conformist** - Adopting upstream model
19. **Open Host Service** - Standardized protocol for integration
20. **Published Language** - Well-documented interchange format

## How It Works

1. **Loads DDD Principles**: Reads comprehensive principle definitions from JSON
2. **Analyzes Code**: Scans your codebase for DDD anti-patterns and code smells
3. **Identifies Issues**: Maps findings to specific DDD principles
4. **Suggests Refactorings**: Provides step-by-step guidance with examples
5. **Applies Changes**: Implements improvements with your approval

## Refactoring Workflow

The plugin follows a systematic approach:

### Phase 1: Discovery
- Understand the domain
- Map bounded contexts
- Identify ubiquitous language usage
- Scan for code smells

### Phase 2: Strategic Planning
- Define clear bounded contexts
- Establish ubiquitous language
- Plan context integration strategies
- Prioritize refactoring areas

### Phase 3: Tactical Implementation
- Apply entity/value object patterns
- Define aggregate boundaries
- Implement domain events
- Create repositories and services
- Layer the architecture properly

### Phase 4: Validation
- Verify domain clarity
- Check consistency boundaries
- Test integration points
- Validate improvements

## Code Smell Detection

The plugin automatically identifies these anti-patterns:

- Anemic domain models (logic in services instead of entities)
- Missing ubiquitous language (technical jargon)
- Blurred bounded contexts
- Mutable value objects
- Large aggregates with weak consistency
- Domain logic in wrong layers
- Missing domain events
- Inappropriate repository patterns
- Bidirectional context dependencies

## Examples

### Before: Anemic Domain Model

```javascript
class Order {
  constructor(id, items) {
    this.id = id;
    this.items = items;
    this.status = 'PENDING';
  }
}

class OrderService {
  cancelOrder(order) {
    if (order.status === 'SHIPPED') {
      throw new Error('Cannot cancel');
    }
    order.status = 'CANCELLED';
  }
}
```

### After: Rich Domain Model

```javascript
class Order {
  constructor(id, items) {
    this.#id = id;
    this.#items = items;
    this.#status = OrderStatus.PENDING;
    this.#events = [];
  }

  cancel() {
    if (!this.canBeCancelled()) {
      throw new OrderCannotBeCancelledException();
    }
    this.#status = OrderStatus.CANCELLED;
    this.#recordEvent(new OrderCancelled(this.#id));
  }

  canBeCancelled() {
    return this.#status !== OrderStatus.SHIPPED;
  }

  #recordEvent(event) {
    this.#events.push(event);
  }
}

class OrderService {
  async cancelOrder(orderId) {
    const order = await this.orderRepository.findById(orderId);
    order.cancel(); // Domain logic stays in domain
    await this.orderRepository.save(order);
  }
}
```

## Plugin Structure

```
plugins/ddd-refactor/
├── .claude-plugin/
│   └── plugin.json              # Plugin metadata
├── commands/
│   └── refactor.md              # /refactor slash command
├── skills/
│   └── ddd-refactor/
│       ├── SKILL.md             # Reusable DDD refactoring skill
│       └── CHECKLIST.md         # Comprehensive anti-pattern checklist
├── agents/
│   └── ddd-analyzer.md          # Autonomous DDD analysis agent
├── hooks/
│   └── pre-commit.md            # Pre-commit DDD checks
├── resources/
│   └── ddd-principles.json      # 19+ DDD principles with guidance
└── README.md                    # This file
```

## Resources

- **DDD Principles JSON**: `resources/ddd-principles.json`
  - Complete principle definitions from Eric Evans' DDD book
  - Code smells and anti-patterns for each principle
  - Step-by-step refactoring guidance
  - Decision trees for pattern selection
  - 19+ strategic and tactical patterns

- **Skills**: `skills/ddd-refactor/`
  - SKILL.md - Complete refactoring workflow
  - CHECKLIST.md - All anti-patterns organized by severity

- **Agents**: `agents/ddd-analyzer.md`
  - Autonomous domain analysis
  - Four-phase assessment process
  - Prioritized findings with roadmap

## Source Material

Principles extracted from:
**"Domain-Driven Design: Tackling Complexity in the Heart of Software"**
by Eric Evans

## Contributing

Contributions welcome! Areas for enhancement:
- Additional DDD patterns
- Language-specific examples
- Integration with testing frameworks
- Automated refactoring tools

## License

MIT

## Author

Radosvet M
Repository: https://github.com/radkomih/claude-code-ext
