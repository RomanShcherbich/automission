# Task 05: Group by Key

## Problem

Implement `group_by_key(items: list[dict], key: str) -> dict`.

## Reference Solution

```python
from collections import defaultdict

def group_by_key(items: list[dict], key: str) -> dict:
    grouped = defaultdict(list)
    for row in items:
        grouped[row[key]].append(row)
    return dict(grouped)
```

## Quick Checks

```python
assert group_by_key([{"a": 1}, {"a": 1}, {"a": 2}], "a") == {1: [{"a": 1}, {"a": 1}], 2: [{"a": 2}]}
assert group_by_key([], "a") == {}
```
