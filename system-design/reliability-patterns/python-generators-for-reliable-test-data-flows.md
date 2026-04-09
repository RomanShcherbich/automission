# Python Generators for Reliable Test Data Flows

## What

A generator is a function that yields values lazily using `yield`.
Instead of building full collections in memory, data is produced on demand.

## Why

For SDET/AQA workflows, generators improve reliability and performance in:
- large data-driven test sets;
- streaming/parsing logs or events;
- setup/teardown lifecycle in pytest fixtures.

This reduces memory pressure and keeps long-running automation stable.

## How

### 1) Basic generator pattern

```python
def count_up_to(n):
    i = 0
    while i < n:
        yield i
        i += 1
```

`count_up_to(3)` returns an iterator object; values are produced one by one.

### 2) Generator expression for efficient transformations

```python
squares = (x * x for x in range(1_000_000))
first = next(squares)  # 0
```

Use this pattern when passing large sequences into `sum`, `any`, `all`, or custom validators.

### 3) Pytest fixture lifecycle with `yield`

```python
import pytest

@pytest.fixture
def db_connection():
    conn = create_connection()   # setup
    yield conn                   # test receives fixture value
    conn.close()                 # teardown
```

Pytest advances the generator before test execution, then resumes it for cleanup.

## Reliability Patterns

- stream data instead of preloading large lists;
- keep generators pure and deterministic;
- separate data production from assertions;
- pair `yield` fixtures with explicit teardown operations;
- avoid hidden side effects inside generator expressions.

## Interview Q&A

**Q1: Why are generators memory-efficient?**  
They emit one value at a time and do not store the full result set.

**Q2: Is `yield` fixture in pytest a generator?**  
Yes. Pytest uses generator protocol for setup (`before yield`) and teardown (`after yield`).

**Q3: When not to use generators?**  
When data must be randomly accessed many times; materialize a list once instead.

