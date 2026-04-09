# Python Interview Cheats (Senior Blocks)

## What

Structured high-density interview blocks for fast revision:
language model, data structures, complexity, generators, concurrency, context managers.

## Why

Senior interviews reward concise, accurate explanations with trade-offs.
Block format is optimized for quick rehearsal before calls.

## How

### Core blocks to rehearse

1. Python runtime model (dynamic typing, GIL, implementations).
2. Data types and mutability/hashing.
3. Collection complexity and when to choose each structure.
4. Generator vs list materialization.
5. Threads/processes/async decision matrix.
6. Context manager protocol and common stdlib examples.

### Fast oral template

- define concept in one sentence;
- give one practical case from automation;
- mention one trade-off and one anti-pattern.

## Q&A

**Q1: How to answer "dict vs list lookup" quickly?**  
`dict` is average O(1) by hash, `list` search is O(n); choose by access pattern.

**Q2: Threads or processes for CPU-bound tasks?**  
Processes, due to GIL limitations in CPython.

**Q3: Why are generators useful in test tooling?**  
They stream data lazily and reduce memory usage in large pipelines.
