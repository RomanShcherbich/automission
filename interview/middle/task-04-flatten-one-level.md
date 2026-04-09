# Task 04: Flatten One Level

## Problem

Implement `flatten_once(items: list) -> list` to flatten nested lists by one level only.

## Reference Solution

```python
def flatten_once(items: list) -> list:
    result = []
    for x in items:
        if isinstance(x, list):
            result.extend(x)
        else:
            result.append(x)
    return result
```

## Quick Checks

```python
assert flatten_once([[1, 2], [3]]) == [1, 2, 3]
assert flatten_once([1, [2, 3], 4]) == [1, 2, 3, 4]
assert flatten_once([[1, [2, 3]], 4]) == [1, [2, 3], 4]
```
