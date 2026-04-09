# macOS Homebrew + GitHub Access Helper

## What

Operational guide for resolving Homebrew tap installation issues on macOS
when repositories are private or API limits/authentication are involved.

## Why

Automation environments on macOS often depend on `brew tap` for internal tools.
Misconfigured GitHub credentials lead to unstable bootstrap scripts and onboarding delays.

## How

### 1) Validate repository first

Before debugging credentials, verify the tap exists and is reachable:

```bash
curl -s https://api.github.com/repos/<org>/<tap-repo>
```

If repository is missing (`404`), fix repository name or installation path first.

### 2) Use secure auth path

Preferred options:
- GitHub credential helper (`osxkeychain`);
- `.netrc` with strict permissions for non-interactive flows.

```bash
git config --global credential.helper osxkeychain
```

For `.netrc`:

```bash
chmod 600 ~/.netrc
```

### 3) Configure Homebrew token for API limits

Use `HOMEBREW_GITHUB_API_TOKEN` for API-rate-limited operations:

```bash
export HOMEBREW_GITHUB_API_TOKEN="<token>"
```

Persist in shell profile only on trusted machine.

### 4) Diagnose common failures

- `Repository not found`: wrong repo name, no access, or deleted repo;
- `Permission denied`: token lacks required scopes;
- `API rate limit exceeded`: token missing or invalid;
- `git clone` fails while API works: git credentials are not configured for `github.com`.

### 5) Install and verify tap

```bash
brew tap <org>/<tap-repo>
brew tap
brew info <formula-name>
```

## Practical Checklist

- repository existence confirmed before auth debugging;
- credential method is documented and reproducible;
- tokens are not hardcoded in scripts or repository files;
- shell profile changes are minimal and reviewed;
- bootstrap script includes verification step after tap install.

## Q&A

**Q1: Why can API calls work but `brew tap` still fail?**  
Because API token and git clone credentials may use different auth paths.

**Q2: Is embedding token into git URL a good default?**  
No. It is a temporary workaround; prefer credential helper or `.netrc`.

**Q3: What is first troubleshooting step?**  
Confirm repository really exists and URL is correct.
