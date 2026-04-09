# Task 01: Parse Log Line

## Problem

Implement `parse_log_line(line: str) -> dict` for format `[LEVEL] message`.

## Expected

- return `{"level": "...", "message": "..."}`;
- assume valid input;
- keep message unchanged except outer spacing.

## Reference Solution

```python
def parse_log_line(line: str) -> dict:
    left, right = line.split("]", 1)
    return {"level": left[1:].strip(), "message": right.strip()}
```

## Quick Checks

```python
assert parse_log_line("[INFO] User logged in") == {"level": "INFO", "message": "User logged in"}
assert parse_log_line("[ERROR] File not found") == {"level": "ERROR", "message": "File not found"}
```
