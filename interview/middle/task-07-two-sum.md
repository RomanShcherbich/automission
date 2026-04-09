# Task 07: Two Sum

## Problem

Implement `two_sum(nums: list[int], target: int) -> tuple[int, int] | None`.

## Reference Solution

```python
def two_sum(nums: list[int], target: int) -> tuple[int, int] | None:
    seen = {}
    for i, x in enumerate(nums):
        need = target - x
        if need in seen:
            return (seen[need], i)
        seen[x] = i
    return None
```

## Quick Checks

```python
assert two_sum([2, 7, 11, 15], 9) == (0, 1)
assert two_sum([3, 2, 4], 6) == (1, 2)
assert two_sum([1, 2, 3], 10) is None
```
