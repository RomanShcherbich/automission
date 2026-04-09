# Programming Principles: SOLID and Composition

## What

Interview-oriented guide to SOLID principles with practical Python examples and
inheritance vs composition decisions.

## Why

AQA/SDET engineers design reusable frameworks and helpers.
SOLID knowledge directly affects maintainability, testability, and extension cost.

## How

### SOLID in one line

- **S**: one responsibility per module;
- **O**: extend behavior without rewriting stable code;
- **L**: subtype must keep parent contract;
- **I**: prefer focused interfaces over fat ones;
- **D**: depend on abstractions, not concrete implementations.

### Inheritance vs composition

- use inheritance for true `is-a` hierarchies with stable contracts;
- use composition for pluggable behavior (clients, repos, loggers, adapters);
- for framework code, composition is usually safer and easier to mock.

### Applied AQA examples

1. inject API client strategy instead of hardcoding requests layer;
2. separate test data builders from assertion helpers;
3. isolate DB access behind repository interface for easier fake/mocks.

## Interview Q&A

**Q1: Why is composition often preferred in automation frameworks?**  
It reduces coupling and simplifies substitution of dependencies in tests.

**Q2: Typical LSP violation example?**  
Subclass changes parent method semantics, breaking callers that rely on base behavior.

**Q3: How does DIP help testability?**  
Business logic accepts abstractions, so test doubles can be injected without infrastructure.
