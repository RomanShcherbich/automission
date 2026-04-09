# Task 06: Merge Two Sorted Lists

## Problem

Implement `merge_sorted(a: list, b: list) -> list` in ascending order.

## Reference Solution

```python
def merge_sorted(a: list, b: list) -> list:
    result = []
    i = j = 0
    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            result.append(a[i])
            i += 1
        else:
            result.append(b[j])
            j += 1
    result.extend(a[i:])
    result.extend(b[j:])
    return result
```

## Quick Checks

```python
assert merge_sorted([1, 3, 5], [2, 4, 6]) == [1, 2, 3, 4, 5, 6]
assert merge_sorted([1, 1, 2], [1, 3]) == [1, 1, 1, 2, 3]
```
