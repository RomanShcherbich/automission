# Sublime Text: Python Indentation Setup

## What

Quick setup guide to enforce 4-space Python indentation in Sublime Text.

## Why

Consistent indentation avoids lint failures and noisy diffs in shared repositories.

## How

### Global setting

Add to user settings:

```json
"tab_size": 4,
"translate_tabs_to_spaces": true
```

### Python-only setting

Create `Python.sublime-settings` in user package folder and use:

```json
{
  "tab_size": 4,
  "translate_tabs_to_spaces": true
}
```

### Current file override

- `View -> Indentation -> Indent Using Spaces`
- `View -> Indentation -> Tab Width -> 4`

## Q&A

**Q1: Why convert tabs to spaces for Python?**  
To avoid inconsistent indentation and parser/lint issues.

**Q2: Should this be global or Python-specific?**  
Python-specific is safer if other languages in your editor use tabs.
