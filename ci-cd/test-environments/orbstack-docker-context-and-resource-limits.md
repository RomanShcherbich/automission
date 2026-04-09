# OrbStack for Local Test Environments (macOS)

## What

OrbStack is a lightweight Docker Desktop alternative for macOS, optimized for Apple Silicon.
For QA automation it is useful as a fast local runtime for API/UI test environments.

## Why

Reliable local CI-like environments reduce "works on my machine" issues.
A stable Docker context and explicit resource limits improve repeatability of test runs.

## How

### 1) Verify Docker context

```bash
docker context ls
docker context use orbstack
docker ps
```

If context points to Docker Desktop socket, switch to OrbStack context before running tests.

### 2) Troubleshoot daemon connectivity

Typical symptom: `Cannot connect to the Docker daemon`.

Quick checks:
- ensure OrbStack app is running;
- verify `DOCKER_HOST` is not pinned to old socket;
- re-check active Docker context.

### 3) Set resource limits for predictable runs

```bash
docker run -m 1g --cpus="1.0" nginx
```

Compose example:

```yaml
services:
  tests:
    image: my-tests:latest
    mem_limit: 1g
    cpus: "1.0"
```

### 4) Validate actual CPU limit

Do not rely on `nproc` inside container for limit checks.
Prefer Docker metadata:

```bash
docker inspect "$CONTAINER_ID" --format='{{.HostConfig.NanoCpus}}'
```

Convert from nano CPUs (`1_000_000_000` = 1 CPU core).

## Practical Checklist

- Docker context is explicitly set to OrbStack;
- compose services have memory/cpu limits for tests;
- test runbook includes context/daemon troubleshooting;
- team knows how to verify real CPU limits via inspect/cgroup;
- fallback path to Docker Desktop is documented.

## Q&A

**Q1: Why can `nproc` show all host cores?**  
Because container visibility can include host cores while cgroup enforces usage quota.

**Q2: Should all test containers be resource-limited?**  
For reproducible CI-like behavior, yes, especially CPU-heavy suites.

**Q3: When to switch back to Docker Desktop?**  
Only if a specific team toolchain depends on Desktop-only features.

