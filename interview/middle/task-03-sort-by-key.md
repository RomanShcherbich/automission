# Task 03: Sort List of Dicts by Key

## Problem

Implement `sort_by_key(items: list[dict], key: str) -> list[dict]` returning a new sorted list.

## Reference Solution

```python
def sort_by_key(items: list[dict], key: str) -> list[dict]:
    return sorted(items, key=lambda d: d[key])
```

## Quick Checks

```python
assert sort_by_key([{"a": 2}, {"a": 1}], "a") == [{"a": 1}, {"a": 2}]
assert sort_by_key([], "a") == []
```
