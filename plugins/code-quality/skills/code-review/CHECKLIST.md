# Clean Code Review Checklist

Complete checklist of all Clean Code principles and best practices for comprehensive code review.

---

## 1. NAMING RULES

### Intent-Revealing Names
- [ ] Names answer why it exists, what it does, and how it is used
- [ ] No name requires a comment to explain it
- [ ] Names specify what is measured and the unit of measurement
- [ ] Avoid mental mapping (single-letter variables except loop counters in short methods)

### Avoid Disinformation
- [ ] No misleading variable names that obscure meaning
- [ ] Don't use "List" unless it's actually a List data structure
- [ ] No similar names that vary in small ways (XyZController vs XyzController)
- [ ] Never use lowercase L or uppercase O as variable names
- [ ] Spell similar concepts similarly; inconsistent spellings are disinformation

### Pronounceable and Searchable Names
- [ ] Names are pronounceable for effective communication
- [ ] No abbreviations that make code hard to discuss (genymdhms)
- [ ] Single-letter names ONLY for local variables in short methods
- [ ] Longer names for larger scopes; searchable names trump constants

### Avoid Noise Words
- [ ] No redundant words: "variable," "table," "object," "data," "info," "bean"
- [ ] Distinguish names meaningfully (moneyAmount vs money is noise)
- [ ] No prefixes like "the" or "a" that add no information

### Naming at Right Abstraction Level
- [ ] Class names are nouns describing responsibilities (not verbs)
- [ ] Method names are verbs describing what they do
- [ ] Names match the level of abstraction

### Consistency
- [ ] Same phrases, nouns, and verbs used across modules
- [ ] Consistent naming tells a story

---

## 2. FUNCTION DESIGN RULES

### Function Size
- [ ] Functions are small (20-30 lines maximum for optimal readability)
- [ ] One entry, one exit point (less critical for small functions)
- [ ] Multiple returns acceptable in small functions

### Single Responsibility Principle (SRP)
- [ ] Each function does ONE thing and does it well
- [ ] Functions handling errors do NOTHING else
- [ ] If try keyword exists, it's the first word; nothing after catch/finally

### Function Arguments
- [ ] Ideal: zero arguments > one > two > three > avoid more than three
- [ ] More than three arguments requires special justification
- [ ] No output arguments; use return values instead
- [ ] No flag arguments (boolean parameters); split into separate functions
- [ ] When >2-3 arguments needed, wrap into argument object/class

### Argument Types
- [ ] Monadic: Ask question, transform, or event handler
- [ ] Dyadic: Has natural ordering or cohesion (Point(x,y))
- [ ] Triadic: Only if absolutely necessary
- [ ] Variadic: Treated as single List argument

### Command Query Separation
- [ ] Functions either DO something (command) OR answer something (query), not both
- [ ] If function changes state, it should not return status

### Descriptive Names
- [ ] Long descriptive names better than short enigmatic ones
- [ ] Descriptive name better than long comment
- [ ] Consistency in module function names

### DRY Principle
- [ ] No duplication; extract duplicated code into separate functions
- [ ] Duplication eliminated (root of all software evil)

---

## 3. CODE STRUCTURE RULES

### Small Classes
- [ ] Classes are small and focused on single responsibility
- [ ] Class size measured by number of methods and cohesion
- [ ] Can derive concise class name from responsibilities

### Cohesion
- [ ] Methods and variables co-dependent and hang together
- [ ] Each method manipulates one or more class variables
- [ ] When methods use subset of variables, consider splitting class

### Class Responsibilities
- [ ] Class has one reason to change (SRP)
- [ ] No ambiguous class names indicating multiple responsibilities
- [ ] More small classes with single responsibilities > fewer large multipurpose classes

### Proximity and Organization
- [ ] Related concepts kept vertically close
- [ ] No forcing readers to hop between files/classes
- [ ] Local variables declared close to usage
- [ ] Instance variables at top of class
- [ ] Control variables declared within loop statement

### Dependent Functions
- [ ] Functions calling each other are vertically close
- [ ] Caller appears above callee when possible
- [ ] Natural flow: definitions follow usage

### Formatting
- [ ] Vertical openness between concepts aids readability
- [ ] Vertical density implies close association
- [ ] File organized like newspaper: headline at top, details increase downward
- [ ] Small files (200 lines average, 500 maximum) easier to understand

---

## 4. ERROR HANDLING RULES

### Exceptions Over Error Codes
- [ ] Use exceptions, not return error codes
- [ ] Exceptions separate error processing from happy path
- [ ] No error code enums that become dependency magnets

### Extract Try/Catch Blocks
- [ ] Try/catch bodies extracted into functions
- [ ] Separation between error handling and normal processing
- [ ] Error handling is ONE thing

### Error Handling Best Practices
- [ ] If function handles errors, it does NOTHING else
- [ ] Try keyword is first word; nothing after catch/finally
- [ ] Use unchecked exceptions to avoid forcing unwanted handling
- [ ] No checked exceptions violating Open/Closed Principle

### Exception Context
- [ ] Exceptions include info about failed operation and failure type
- [ ] Messages indicate intent of failure
- [ ] Exceptions defined by caller's needs, not implementation details

---

## 5. COMMENTS RULES

### Core Philosophy
- [ ] Comments are necessary evils; prefer expressing in code
- [ ] If code confusing, fix code, don't comment it
- [ ] No inaccurate or outdated comments (worse than no comments)
- [ ] No comments that make up for bad code

### Express in Code
- [ ] Intent expressed through function/variable naming
- [ ] Well-chosen name for small function > comment header
- [ ] Most intent expressible in code with better naming

### Good Comments (Acceptable)
- [ ] Legal comments (copyright/authorship) reference external docs
- [ ] Informative comments for complex logic (rare, prefer naming)
- [ ] Explanation of intent/rationale for decisions
- [ ] Clarification of obscure standard library code
- [ ] TODO comments with resolution plan

### Bad Comments (Flag for Removal)
- [ ] No redundant comments restating code
- [ ] No misleading comments about intent/operation
- [ ] No commented-out code (use version control)
- [ ] No noise comments adding no value
- [ ] No journal comments (version control handles history)
- [ ] No HTML in comments
- [ ] No Javadoc for internal code

---

## 6. SOLID PRINCIPLES RULES

### Single Responsibility Principle (SRP)
- [ ] Class/module has ONE reason to change
- [ ] No multiple responsibilities
- [ ] Concise name derivable from single responsibility

### Open/Closed Principle (OCP)
- [ ] Software open for extension, closed for modification
- [ ] No functions changing whenever new types added
- [ ] Exception-based error handling (OCP compliant)

### Dependency Inversion Principle (DIP)
- [ ] Depend on abstractions, not concrete implementations
- [ ] High-level modules don't depend on low-level modules
- [ ] Both depend on abstractions

### Liskov Substitution Principle
- [ ] Derived types substitutable for base types
- [ ] No invalid type checking throughout codebase

### Interface Segregation
- [ ] Clients not forced to depend on unused interfaces

---

## 7. TESTING RULES

### Three Laws of TDD
- [ ] No production code until failing unit test written
- [ ] No more unit test than sufficient to fail
- [ ] No more production code than sufficient to pass test

### Test Quality
- [ ] Tests as clean/maintainable as production code
- [ ] Tests express intent clearly (domain-specific language)
- [ ] Tests run quickly (fast feedback loop)

### Test Structure
- [ ] One assert per test (or single concept per test minimum)
- [ ] Each test verifies ONE thing
- [ ] Test readability like function descriptions

### Test Coverage
- [ ] All tests run before check-in
- [ ] Boundary conditions tested
- [ ] Exhaustive tests near previous bugs
- [ ] No skipped trivial tests without confidence
- [ ] Ignored tests document ambiguities

---

## 8. OBJECT-ORIENTED DESIGN RULES

### Objects vs Data Structures
- [ ] Objects hide data, expose operations
- [ ] Data structures expose data, hide operations
- [ ] No hybrids (worst of both worlds)
- [ ] Clear distinction: procedural vs OO approach

### Law of Demeter
- [ ] Methods only call methods of:
  - Its own class
  - Objects it creates
  - Objects passed as arguments
  - Objects in instance variables
- [ ] No chaining calls (train wrecks): obj.getA().getB().getC()

### Data Transfer Objects
- [ ] DTOs are data structures (public variables, no functions)
- [ ] Useful for database communication, message parsing
- [ ] Beans (getters/setters) provide little benefit over public fields

### Design Clarity
- [ ] Not everything is an object
- [ ] Choose procedural vs OO based on problem domain
- [ ] No muddled hybrid designs

---

## 9. CONCURRENCY RULES

### Synchronization
- [ ] Synchronized sections kept small
- [ ] Lock contention minimized
- [ ] Deadlock possibility reduced

### Concurrency Design
- [ ] What gets done decoupled from when it gets done
- [ ] Design accommodates concurrency
- [ ] Shared objects and scope minimized

### Thread Safety
- [ ] Clear understanding of shared vs non-shared
- [ ] Narrow scope of shared data
- [ ] Design accommodates clients vs forcing client state management

---

## 10. CODE FORMATTING RULES

### Purpose
- [ ] Formatting is about communication (first order of business)
- [ ] Readability affects all future changes

### Spacing
- [ ] Vertical spacing separates concepts
- [ ] Vertical density shows association
- [ ] Horizontal spacing improves readability
- [ ] Spaces around operators and between parameters

### Indentation and Structure
- [ ] Consistent indentation showing scope/hierarchy
- [ ] File size: 200 lines average, 500 maximum
- [ ] Newspaper metaphor: headline → details

---

## 11. ADDITIONAL CORE RULES

### Polymorphism
- [ ] Use polymorphism for type handling vs switch statements
- [ ] Switch acceptable if used once to create polymorphic objects
- [ ] Switch hidden behind inheritance

### DRY Principle
- [ ] No repeated code anywhere
- [ ] Duplication is root cause of problems
- [ ] Extract common logic into reusable functions

### Structured Programming
- [ ] Single entry/exit in large functions
- [ ] Multiple returns OK in small functions
- [ ] No goto statements

### Scope and Variables
- [ ] Minimize variables in scope
- [ ] Reduce instance variables tracked by class
- [ ] Keep functions focused and small

### Avoid Overengineering
- [ ] No abstractions for one-time operations
- [ ] Don't design for hypothetical future requirements (YAGNI)
- [ ] Three similar lines of code better than premature abstraction
- [ ] No unnecessary layers, wrappers, or indirection
- [ ] No helpers, utilities, or factories for single use cases
- [ ] Only create what's needed for current requirements
- [ ] Simplest solution that works is preferred
- [ ] No feature flags or backward-compatibility for unused features
- [ ] Don't add configurability that isn't currently needed
- [ ] No error handling for scenarios that can't happen
- [ ] Trust internal code and framework guarantees
- [ ] Only validate at system boundaries (user input, external APIs)
- [ ] Avoid "just in case" code that addresses no real requirement

### Performance and Efficiency
- [ ] No premature optimization (optimize after measuring)
- [ ] Identify actual bottlenecks through profiling before optimizing
- [ ] No N+1 query problems (batch database/API calls)
- [ ] Use appropriate data structures for access patterns (Map vs Array, Set vs Array)
- [ ] Minimize unnecessary object creation in hot paths/loops
- [ ] Cache expensive computations when appropriate (memoization)
- [ ] Avoid nested loops with high complexity (O(n²) or worse) when avoidable
- [ ] Consider algorithmic complexity for operations on large datasets
- [ ] Lazy load expensive resources (defer until needed)
- [ ] Release resources promptly (connections, file handles, memory)
- [ ] Avoid redundant calculations (compute once, reuse result)
- [ ] Use efficient string operations (StringBuilder vs concatenation in loops)
- [ ] Index database queries on frequently searched columns
- [ ] Paginate or stream large result sets instead of loading all at once
- [ ] Avoid blocking operations in main/UI threads

### Consistency and Communication
- [ ] Consistent conventions throughout codebase
- [ ] Team agrees on single formatting ruleset
- [ ] Code written for readers, not compilers
- [ ] Intent clearly communicated
- [ ] Obvious what code does

---

## REVIEW PROCESS

For each violation found:
1. **Identify**: Cite specific file path and line numbers
2. **Explain**: Reference which principle is violated and why it matters
3. **Suggest**: Provide concrete, actionable improvement with example
4. **Prioritize**: Categorize as Critical / High / Medium / Low

### Additional Checks
- [ ] Look for duplicated/similar functionality to consolidate
- [ ] Verify logs accurately reflect execution flow
- [ ] Check logs aid debugging and provide clarity
- [ ] Ensure no overengineering or unnecessary complexity
- [ ] Verify business logic meets acceptance criteria
- [ ] Confirm architectural adherence to patterns
- [ ] Identify security vulnerabilities
- [ ] Assess performance efficiency

**Double-check findings to ensure no false positives.**
