# Unified HTTP Client for Multi-Project AQA Teams

## What

A shared HTTP client layer for API test automation across multiple projects and teams.
The client provides a standard interface for `GET/POST/PUT/DELETE`, reusable request builders,
response handling, and pytest integration points.

## Why

When different AQA engineers work on different APIs, duplicated client logic creates:
- inconsistent request patterns;
- weak error handling;
- fragmented logging and debugging.

A unified client reduces maintenance cost and accelerates onboarding.

## How

### Core architecture

- `core client`: transport + retries + auth headers;
- `request builders`: payload/query/header composition;
- `response adapter`: parse JSON/XML and map API errors to domain exceptions;
- `pytest fixtures`: provide project-aware client instances;
- `CI templates`: run tests with environment-specific config.

### Recommended package layout

```text
http-client-lib/
├── client/              # base HTTP operations
├── builders/            # payload and request builders
├── response/            # parsing and error mapping
├── pytest_plugin/       # fixtures and CLI options
├── examples/            # sample test integrations
└── pyproject.toml       # packaging metadata
```

### Minimal usage sketch

```python
def test_get_user(api_client):
    response = api_client.get("/users/1")
    assert response.status_code == 200
    assert response.json()["id"] == 1
```

### Team-level delivery flow

1. Build feature in branch with tests.
2. Bump package version (semantic versioning).
3. Publish artifact to internal registry.
4. Update consuming test projects to new package version.
5. Validate in CI with smoke and regression suites.

## Practical Checklist

- standard request/response contract documented;
- fixture-based client initialization in pytest;
- clear logging for request and response payloads;
- CI jobs for package build and consumer validation;
- versioning policy enforced (`MAJOR.MINOR.PATCH`).

## Q&A

**Q1: Should each project still have custom wrappers?**  
Only thin wrappers for domain-specific endpoints; transport and common behavior stay in shared library.

**Q2: What to do with different auth schemes?**  
Use pluggable auth strategy per project while keeping the same client interface.

**Q3: Where to keep builder pattern logic?**  
In dedicated `builders` module with tested reusable constructors.

