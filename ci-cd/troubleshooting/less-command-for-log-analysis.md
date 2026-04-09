# `less` Command for CI/CD Log Analysis

## What

`less` is an interactive pager for navigating large text files in terminal.
It is essential for reading long CI logs, test reports, and container output.

## Why

During incident/debug workflows, full logs are often too large for plain terminal output.
`less` enables fast navigation and targeted search without loading everything in an editor.

## How

### Open a file

```bash
less build.log
```

### Core navigation keys

- `f`: next page
- `b`: previous page
- `j` / `k`: one line down/up
- `/text`: search forward
- `n`: next match
- `N`: previous match
- `h`: help
- `q`: quit

### Practical CI use cases

1. Inspect failing test section in `pytest` output.
2. Search for first error marker in pipeline logs (`/ERROR`, `/Traceback`, `/FAILED`).
3. Validate retry behavior by navigating repeated job blocks.

## Q&A

**Q1: Why not just use `cat` for logs?**  
`cat` dumps everything at once; `less` provides interactive navigation and search.

**Q2: What is fastest way to find repeated failures?**  
Use `/pattern` once and traverse with `n`/`N`.

**Q3: Is `less` only for local files?**  
No. It can read piped output, for example: `docker logs container | less`.

