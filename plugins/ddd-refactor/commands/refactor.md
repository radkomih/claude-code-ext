---
description: Refactor codebase using Domain-Driven Design principles
allowed-tools: Read, Grep, Glob, Bash, Edit, Write
argument-hint: [path or description]
---

# DDD Refactoring Assistant

Refactor code using Domain-Driven Design principles from Eric Evans' book.

## Scope

Target: ${ARGUMENTS:-"the entire codebase"}

## Strategy

Use the **ddd-refactor skill** to conduct thorough DDD analysis and refactoring with:

1. **Four-phase approach**:
   - Discovery & Analysis (understand domain, identify code smells)
   - Strategic Planning (bounded contexts, ubiquitous language)
   - Tactical Implementation (entities, value objects, aggregates, etc.)
   - Validation & Testing

2. **Focus areas**:
   - Bounded context boundaries
   - Ubiquitous language usage
   - Anemic domain models
   - Entity/Value Object patterns
   - Aggregate design
   - Domain events
   - Repository patterns
   - Layered architecture
   - Anti-corruption layers

## Instructions

1. **Identify scope**: Determine what to refactor (specific module vs full codebase)
2. **Use git for context**: Run `git log` to understand recent changes if applicable
3. **Apply the skill**: Use the ddd-refactor skill which contains:
   - Complete DDD principles from `ddd-principles.json`
   - Anti-pattern detection checklist
   - Refactoring guidance with examples
4. **Provide specific feedback**:
   - File paths and line numbers (e.g., `src/domain/Order.java:45`)
   - What anti-pattern exists and which DDD principle applies
   - Why it matters for domain clarity
   - How to fix it with before/after code examples
5. **Prioritize findings**:
   - **Critical**: Domain logic in wrong layers, broken invariants
   - **High**: Anemic models, missing aggregates, no domain events
   - **Medium**: Primitive obsession, naming issues, service bloat
   - **Low**: Minor refactoring opportunities

## Output Format

Organize findings by priority with clear, actionable refactoring steps.

For each issue include:
- Location (file:line)
- Anti-pattern description
- DDD principle to apply
- Step-by-step refactoring guidance
- Concrete code example showing before/after

## Begin Refactoring

The ddd-refactor skill will guide you through the complete refactoring process:

1. Load DDD principles from `resources/ddd-principles.json`
2. Analyze the specified target for anti-patterns
3. Identify code smells using the comprehensive checklist
4. Propose refactorings with before/after examples
5. Apply changes with your approval

Start now by invoking the ddd-refactor skill to analyze the specified target.
