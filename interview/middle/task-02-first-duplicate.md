# Task 02: First Duplicate

## Problem

Return first element that appears twice while scanning left to right.
If none exists, return `None`.

## Reference Solution

```python
def first_duplicate(items: list):
    seen = set()
    for x in items:
        if x in seen:
            return x
        seen.add(x)
    return None
```

## Quick Checks

```python
assert first_duplicate([1, 2, 3, 2, 1]) == 2
assert first_duplicate(["a", "b", "a"]) == "a"
assert first_duplicate([1, 2, 3]) is None
```
