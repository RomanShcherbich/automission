# Go REST API Test Project Structure

## What

Reference architecture for a scalable REST API test automation project in Go.
Focus: predictable structure, reusable helpers, and CI-friendly execution.

## Why

As test suites grow, inconsistent folder layout increases maintenance cost and slows onboarding.
A stable structure keeps test code discoverable and reduces duplication across API modules.

## How

### Recommended repository layout

```text
rest-api-testing/
├── config/          # environment and runtime settings
├── tests/           # API tests grouped by business domain
├── helpers/         # HTTP/JSON/assert helper functions
├── data/            # fixtures and payload templates
├── docker/          # local test environment bootstrap
├── .env             # local variables
├── Makefile         # run/lint/bootstrap commands
└── README.md        # usage documentation
```

### Key design decisions

1. Keep `tests/` domain-oriented (`users`, `auth`, `products`) instead of endpoint-oriented.
2. Move reusable request logic to `helpers/http_client.go`.
3. Store repeatable fixtures in `data/` to avoid hardcoded payload duplication.
4. Use `config/` for environment-driven settings (`API_BASE_URL`, auth tokens, timeouts).
5. Keep local infra reproducible via `docker/docker-compose.yml`.

### Minimal test flow

```go
endpoint := config.BaseURL + "/users/1"
resp, err := helpers.Get(endpoint)
if err != nil {
    t.Fatalf("request failed: %v", err)
}
if resp.StatusCode != http.StatusOK {
    t.Fatalf("status=%d want=%d", resp.StatusCode, http.StatusOK)
}
```

### Tooling baseline

- Standard `testing` for fast unit/integration checks.
- `testify` for readable assertions.
- `resty` if advanced HTTP client features are needed.
- `gomock` for external dependency seams.

## Practical Checklist

- configuration is environment-driven, not hardcoded;
- test data is reusable and versioned;
- suites are deterministic and independent;
- local run and CI run use equivalent commands;
- README includes runbook for adding new scenarios.

## Q&A

**Q1: Should I keep one huge `api_test.go`?**  
No. Split by domain/module to keep ownership and review boundaries clear.

**Q2: Where to keep expected API responses?**  
In `data/` fixtures or builder functions, not inline across many tests.

**Q3: When to introduce Docker in API tests?**  
When tests depend on runtime services (DB, queue, auth provider) and need reproducible local/CI environments.

