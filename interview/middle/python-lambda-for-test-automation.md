# Python Lambda for Test Automation (Middle)

## What

`lambda` is an anonymous one-line function in Python:

```python
lambda args: expression
```

In QA automation it is useful for short callbacks in sorting, filtering, mapping, and lightweight key selectors.

## Why

Interview tasks for SDET/AQA frequently include:
- sorting complex test data by field;
- extracting values from API payloads;
- filtering datasets before assertions.

Knowing when to use `lambda` keeps code concise while preserving readability in simple transformations.

## How

### 1) Sort test objects by a field

```python
items = [
    {"name": "Bob", "age": 30},
    {"name": "Alice", "age": 25},
]

sorted_items = sorted(items, key=lambda d: d["age"])
```

### 2) Filter only valid entities for assertion

```python
records = [{"id": 1, "active": True}, {"id": 2, "active": False}]
active = list(filter(lambda r: r["active"], records))
```

### 3) Multi-key sorting in deterministic tests

```python
users = [
    {"name": "Bob", "age": 25},
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 30},
]

sorted_users = sorted(users, key=lambda u: (u["age"], u["name"]))
```

## Interview Q&A

**Q1: Why not always use `lambda`?**  
If logic is longer than one expression or reused in multiple places, use `def` with a descriptive name.

**Q2: Where is `lambda` most useful in test code?**  
In `sorted(key=...)`, `map()`, and `filter()` when transformation is short and local.

**Q3: What is common anti-pattern?**  
Nested or overly complex lambda expressions that hurt readability and debugging.

## Task Variants

1. Sort API response by `created_at` and then by `id`.
2. Filter orders with `status == "PAID"` and assert count.
3. Build a transformed list of user emails in lower case for snapshot comparison.

