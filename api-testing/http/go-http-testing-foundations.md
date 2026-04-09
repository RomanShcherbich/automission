# Go HTTP Testing Foundations for AQA

## What

Practical overview of Go testing stack for HTTP/API automation:
- standard `testing` package;
- `net/http/httptest` for handler testing;
- `testify` for readable assertions;
- `gomock` for interaction-based tests in integration boundaries.

## Why

SDET/AQA engineers often need fast, deterministic API checks in CI without full environment startup.
Go offers lightweight tooling to validate handlers, contracts, and behavior with minimal runtime overhead.

## How

### 1) Start with the standard test package

Use `_test.go` files and table-driven tests for predictable coverage of HTTP behavior.

```go
package handlers

import "testing"

func TestNormalizeStatus(t *testing.T) {
	cases := []struct {
		in   int
		want int
	}{
		{200, 200},
		{201, 200},
		{404, 400},
	}

	for _, tc := range cases {
		got := normalizeStatus(tc.in)
		if got != tc.want {
			t.Fatalf("normalizeStatus(%d) = %d, want %d", tc.in, got, tc.want)
		}
	}
}
```

### 2) Test HTTP handlers with `httptest`

Use request/recorder pairs to validate status code, headers, and response body.

```go
package handlers

import (
	"net/http"
	"net/http/httptest"
	"testing"
)

func TestHealthHandler(t *testing.T) {
	req := httptest.NewRequest(http.MethodGet, "/health", nil)
	rr := httptest.NewRecorder()

	HealthHandler(rr, req)

	if rr.Code != http.StatusOK {
		t.Fatalf("status = %d, want %d", rr.Code, http.StatusOK)
	}
}
```

### 3) Add expressive assertions with `testify`

Prefer for larger suites where readable failure output improves investigation speed.

```go
import (
	"testing"

	"github.com/stretchr/testify/assert"
)

func TestBuildURL(t *testing.T) {
	got := BuildURL("api", "v1", "users")
	assert.Equal(t, "/api/v1/users", got)
}
```

### 4) Use mocks only at external boundaries

Apply `gomock` for dependencies like external services, queue clients, or feature flags.
Avoid over-mocking pure business logic.

### 5) CI-ready checklist

- keep tests isolated from external state;
- ensure deterministic test data;
- run fast unit/handler tests on each PR;
- reserve heavy E2E flows for dedicated pipelines;
- fail on contract-breaking API response changes.

## Q&A

**Q1: When to use `httptest` vs full end-to-end test?**  
Use `httptest` for handler logic and response contracts; use E2E for cross-service integration and real infrastructure behavior.

**Q2: Should every API test use mocks?**  
No. Mock only external boundaries. For pure logic prefer direct tests without mocks.

**Q3: What is the minimum HTTP test contract?**  
At least status code, required headers, and key response fields.

