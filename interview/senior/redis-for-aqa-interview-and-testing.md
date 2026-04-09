# Redis for AQA: Senior Interview and Testing Guide

## What

Redis is an in-memory key-value data store used for caching, sessions, counters, queues,
pub/sub messaging, and fast state lookup.

## Why

In test automation, Redis often sits between API and database layers.
Senior AQA engineers need to validate cache correctness, TTL behavior, and state isolation in integration tests.

## How

### Core concepts to explain in interview

- data is memory-first with optional persistence (RDB/AOF);
- commands are key-oriented, not SQL queries;
- supports multiple structures: `String`, `List`, `Set`, `ZSet`, `Hash`, `Stream`;
- very low latency, commonly used as cache/session/rate-limit backend.

### Persistence trade-offs

- `RDB`: periodic snapshots, faster recovery startup, possible data loss between snapshots;
- `AOF`: append-only command log, better durability, larger disk footprint;
- common production setup uses both.

### TTL and cache validation

Important commands:
- `EXPIRE`, `PEXPIRE`, `TTL`
- `SETEX`

Typical test checks:
1. Key appears after action.
2. TTL is set and within expected range.
3. Key expires or gets invalidated after source data change.

### Practical debugging commands

```bash
redis-cli -h <host> -p 6379
SCAN 0 MATCH user:* COUNT 100
TYPE user:123
TTL user:123
INFO memory
```

Prefer `SCAN` over `KEYS` on large datasets.

### Integration-test patterns

1. Run isolated Redis instance (container) per test suite.
2. Clean state before/after tests (`FLUSHDB` or targeted key deletion).
3. Use deterministic key namespaces per test run.
4. Validate both API response and Redis side effects.

## Interview Q&A

**Q1: Why can cache-related tests be flaky?**  
Because TTL windows, async invalidation, and shared Redis state create timing and isolation issues.

**Q2: When do you choose mock Redis vs real Redis in tests?**  
Use mock for pure logic/unit tests, real Redis for integration behavior (TTL, persistence, command semantics).

**Q3: What is a common anti-pattern in Redis test setup?**  
Using shared persistent Redis across parallel tests without namespace isolation or cleanup.
