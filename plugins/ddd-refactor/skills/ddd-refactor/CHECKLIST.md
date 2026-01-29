# DDD Anti-Pattern Checklist

Comprehensive checklist for identifying Domain-Driven Design violations and anti-patterns.

## Strategic Design Anti-Patterns

### Ubiquitous Language
- [ ] Technical names instead of domain terminology (e.g., `DataProcessor` vs `OrderFulfillment`)
- [ ] Multiple terms for same concept across codebase
- [ ] Domain experts don't understand the code vocabulary
- [ ] Comments needed to translate code terms to business terms
- [ ] Generic method names (`process()`, `handle()`, `doStuff()`)

### Bounded Context
- [ ] No explicit context boundaries defined
- [ ] Same entity meaning different things in different places
- [ ] Code fragmentation with duplicate concepts
- [ ] Unclear ownership between teams
- [ ] Database schema conflicts from merged models
- [ ] Integration code scattered throughout system

### Context Mapping
- [ ] No documented context relationships
- [ ] Missing anti-corruption layers for external systems
- [ ] Shared database between contexts without explicit strategy
- [ ] Bidirectional dependencies between contexts
- [ ] No translation at context boundaries

## Tactical Pattern Anti-Patterns

### Entity
- [ ] Entities without clear identity (using arbitrary IDs)
- [ ] Anemic entities (only getters/setters, no behavior)
- [ ] Business logic in services instead of entities
- [ ] Entities using value equality instead of identity equality
- [ ] Public setters allowing invariant violations
- [ ] No lifecycle management (creation, state transitions, deletion)

### Value Object
- [ ] Primitive obsession (using strings/numbers instead of value objects)
- [ ] Mutable value objects
- [ ] Value objects with identity
- [ ] No validation in value object constructors
- [ ] Value objects using reference equality instead of value equality
- [ ] Value objects with setters

### Aggregate
- [ ] No clear aggregate boundaries
- [ ] Large aggregates (>5-7 entities)
- [ ] Direct access to aggregate internals
- [ ] Multiple aggregate roots updated in single transaction
- [ ] Weak invariant enforcement
- [ ] Aggregates holding references to other aggregates (should use IDs)
- [ ] No aggregate root controlling access to internal entities

### Repository
- [ ] Repositories for non-aggregate root entities
- [ ] Repositories returning DTOs or raw data instead of domain objects
- [ ] Domain logic in repository methods
- [ ] Repositories exposing internal aggregate entities
- [ ] Query methods mixed with persistence concerns
- [ ] Infrastructure concerns leaking into repository interface

### Factory
- [ ] Complex construction logic in constructors
- [ ] New operator used directly for complex aggregates
- [ ] No validation during object creation
- [ ] Factory methods spread across codebase
- [ ] Factories that don't ensure valid initial state

### Domain Service
- [ ] Fat services containing all business logic
- [ ] Stateful domain services (should be stateless)
- [ ] Services named generically (`Manager`, `Helper`, `Util`)
- [ ] Application services in domain layer
- [ ] Infrastructure services mixed with domain services
- [ ] Domain logic that should be in entities placed in services

### Domain Event
- [ ] No domain events for significant state changes
- [ ] Events named with technical terms instead of domain language
- [ ] Events carrying behavior instead of just data
- [ ] Synchronous coupling where events should be used
- [ ] Missing event sourcing for audit requirements
- [ ] Events exposed outside bounded context without translation

### Module/Package
- [ ] Modules organized by technical layers instead of domain concepts
- [ ] Low cohesion (unrelated concepts together)
- [ ] High coupling between modules
- [ ] Cyclic dependencies between modules
- [ ] No clear module ownership

## Architecture Anti-Patterns

### Layered Architecture
- [ ] Domain logic in presentation layer (controllers, views)
- [ ] Domain logic in infrastructure layer (database, messaging)
- [ ] Infrastructure concerns in domain layer (SQL, HTTP, etc.)
- [ ] Application services containing business rules
- [ ] Outward dependencies from domain layer
- [ ] No clear layer boundaries

### Anti-Corruption Layer
- [ ] Direct integration with external systems in domain code
- [ ] External data models contaminating domain model
- [ ] No translation layer for legacy systems
- [ ] Third-party DTOs used in domain layer
- [ ] External API changes breaking domain code

### Dependency Management
- [ ] Domain layer depending on infrastructure
- [ ] Circular dependencies between layers
- [ ] No dependency inversion at layer boundaries
- [ ] Concrete implementations instead of interfaces in domain
- [ ] Framework-specific annotations in domain layer

## Additional Code Smells

### Transaction Boundaries
- [ ] Transactions spanning multiple aggregates
- [ ] Long-running transactions
- [ ] No transaction boundaries defined
- [ ] Inconsistent transaction management

### Performance
- [ ] N+1 query problems from aggregate traversal
- [ ] Lazy loading causing performance issues
- [ ] Large aggregates causing memory issues
- [ ] No caching strategy for frequently accessed aggregates

### Testing
- [ ] Difficulty unit testing domain logic
- [ ] Tests requiring database for domain logic
- [ ] Mock-heavy tests indicating poor boundaries
- [ ] Integration tests used where unit tests should suffice
- [ ] Domain logic tested only through UI/API tests

### Data Model
- [ ] Database schema driving domain model
- [ ] Object-relational impedance mismatch issues
- [ ] No clear mapping strategy (aggregates to tables)
- [ ] Bidirectional associations everywhere
- [ ] Cascading deletes not matching aggregate boundaries

## Common Violations by File Type

### Controllers/API Layer
```
❌ Bad: Business logic in controller
✅ Good: Thin controller delegating to application service
```

### Service Classes
```
❌ Bad: OrderService with 2000 lines of business logic
✅ Good: Order entity with logic, OrderService orchestrates
```

### Entity Classes
```
❌ Bad: Entity with only getters/setters
✅ Good: Entity with behavior and invariant enforcement
```

### Repository Classes
```
❌ Bad: OrderLineRepository (OrderLine is not aggregate root)
✅ Good: OrderRepository (Order is aggregate root)
```

## Severity Levels

### Critical (Block commit/merge)
- Domain logic in presentation/infrastructure layers
- Missing aggregate boundaries causing data inconsistency
- No anti-corruption layer for critical external integration
- Public setters breaking invariants on core entities

### High (Fix before deployment)
- Anemic domain model in core domain
- Missing domain events for audit trail
- Large aggregates causing performance issues
- Repositories for non-aggregate roots

### Medium (Fix in next sprint)
- Primitive obsession instead of value objects
- Missing ubiquitous language in non-core areas
- Services that should be entity methods
- Unclear bounded context boundaries

### Low (Technical debt)
- Minor naming inconsistencies
- Missing factory methods for complex creation
- Value objects that could be more immutable
- Module organization improvements

## Quick Reference

### Entity Checklist
1. Has clear identity (not arbitrary auto-increment)
2. Implements identity-based equality
3. Contains business behavior (not just data)
4. Enforces its own invariants
5. Has clear lifecycle management

### Value Object Checklist
1. Immutable (no setters)
2. Value-based equality
3. Self-validating
4. Side-effect free
5. Replaceable (not modifiable)

### Aggregate Checklist
1. Has clear root entity
2. Enforces consistency boundary
3. Internal entities not accessible outside
4. Small (2-5 entities max)
5. Updates through root only

### Repository Checklist
1. One repository per aggregate root
2. Returns fully reconstituted aggregates
3. No domain logic in repository
4. Uses specifications for complex queries
5. Interface in domain, implementation in infrastructure

### Service Checklist
1. Stateless
2. Contains operations not belonging to entities
3. Clearly domain/application/infrastructure type
4. Thin (orchestration, not logic)
5. Named with domain language

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
- Architecture reviews
- Domain modeling workshops
- Onboarding new team members

For each identified issue, reference the specific principle from `ddd-principles.json` for detailed refactoring guidance.
