# VSCode Go Setup for Automation Repositories

## What

A baseline VSCode configuration for Go-based automation projects:
- Go extension and language tooling;
- workspace settings for formatting/linting/build;
- build task for quick local verification.

## Why

Consistent editor setup reduces style drift, missed imports, and local build failures.
For distributed AQA teams this makes Go test tooling predictable across machines.

## How

### 1) Install prerequisites

```sh
brew install go
```

Install VSCode extension:
- Go (`golang.go`)

### 2) Configure `.vscode/settings.json`

```json
{
  "gopls": {
    "ui.semanticTokens": true
  },
  "editor.codeActionsOnSave": {
    "source.organizeImports": "always"
  },
  "editor.defaultFormatter": "golang.go",
  "go.useLanguageServer": true,
  "go.buildOnSave": "workspace",
  "go.lintOnSave": "workspace",
  "go.vetOnSave": "workspace"
}
```

### 3) Configure `.vscode/tasks.json`

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "build",
      "type": "shell",
      "command": "go",
      "args": ["build", "-o", "bin/goapp", "main.go"],
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "problemMatcher": ["$go"]
    }
  ]
}
```

### 4) Team checks

- formatter and import organization run on save;
- lint/vet findings are visible before commit;
- build task works from clean clone;
- settings are documented in repository onboarding.

## Q&A

**Q1: Is `gopls` required?**  
Yes for modern Go workflows (navigation, diagnostics, refactor quality).

**Q2: Why keep workspace-level settings in repo docs?**  
To avoid configuration drift between contributors.

**Q3: Is this CI config?**  
No. It is local developer tooling that lowers CI failure rate.
