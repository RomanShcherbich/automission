# Python Development Workflow for Test Repositories

## What

Compact workflow for preparing Python automation projects: environment setup,
cleanup, packaging, and release tagging.

## Why

Standardized local workflow reduces environment drift and broken CI outcomes.

## How

### Environment bootstrap

```bash
python -m venv .venv
python3 -m pip install -U pip setuptools wheel
python3 -m pip install .
```

Use explicit interpreter path when multiple versions are installed.

### Cache cleanup

```bash
find . | grep -E "(/__pycache__$|pytest_cache|\\.pyc$|\\.pyo$)" | xargs rm -rf
```

### Release tag basics

```bash
git tag -a v1.4 -m "Release version 1.4"
git push origin v1.4
```

Annotated tags are preferred for release metadata and traceability.

## Q&A

**Q1: Why isolate with venv for test repos?**  
To keep dependency graph deterministic across contributors and CI workers.

**Q2: When use annotated tags instead of lightweight tags?**  
For releases where metadata and auditability matter.

**Q3: Why clean caches in troubleshooting flow?**  
To remove stale bytecode and pytest artifacts that can mask real changes.
