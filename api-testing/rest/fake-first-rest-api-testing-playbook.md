# Fake-First REST API Testing Playbook (Go)

## What

Fake-first testing is an API automation approach where you design a fake/mock service first,
then build and validate tests against that controlled contract before wiring to real endpoints.

## Why

This approach accelerates local development and reduces flakiness by:
- decoupling test authoring from unstable environments;
- making API contract assumptions explicit;
- enabling fast feedback in early implementation stages.

## How

### Iteration cycle

1. Create a fake endpoint for target scenario.
2. Add configuration switch (`fake` vs `real` backend).
3. Update `Makefile` and compose scripts for fake runtime.
4. Implement/adjust tests against fake behavior and edge cases.
5. Run full suite and verify no regressions.

### Suggested project layout

```text
rest-api-testing/
├── config/       # backend mode and base URLs
├── mocks/        # fake endpoints and third-party doubles
├── tests/        # API tests by domain
├── helpers/      # http/json/assert helpers
├── data/         # request/response fixtures
├── docker/       # fake-enabled local stack
└── Makefile      # fake/real run targets
```

### Minimal config switch example

```go
mode := os.Getenv("API_MODE") // fake | real
baseURL := os.Getenv("API_BASE_URL")

if mode == "fake" {
    baseURL = os.Getenv("FAKE_API_BASE_URL")
}
```

### Team operating rules

- fake responses must mirror documented contract shape;
- each fake scenario must include negative branch (4xx/5xx);
- test names should indicate backend mode assumptions;
- merge only when same test passes in fake and real modes (where applicable).

## Q&A

**Q1: Is fake-first equal to replacing integration tests?**  
No. It complements integration tests; final confidence still requires real dependency validation.

**Q2: What is the biggest anti-pattern?**  
Letting fake contracts diverge from real API without synchronization.

**Q3: When is fake-first most useful?**  
During feature bootstrap, unstable test environments, and contract-first collaboration.

