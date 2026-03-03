---
title: Performance tuning
weight: 8

### FIXED, DO NOT MODIFY
layout: learningpathall
---

Tune with architecture-specific reasoning, not one shared profile.

## Manual workflow (required)

Start from endpoint throughput and latency, not from a single `/install` metric.

Test candidate settings one at a time to isolate their effects:

| Variable | What it does |
|----------|-------------|
| `DOTNET_ThreadPool_ForceMinWorkerThreads=2` | Pre-allocates worker threads to avoid cold-start ramp-up latency. |
| `COMPlus_ThreadPool_UnfairSemaphoreSpinLimit=0` | Disables thread pool semaphore spin-waits. On Arm, spinning can be less efficient than on x86 due to weaker memory ordering. |
| `DOTNET_gcServer=1` | Uses one GC heap per logical core instead of a shared heap. Reduces GC pause contention under parallel workloads. |

Validated sweep results on Arm (same test harness, `wrk -t2 -c32 -d10s`):

| Arm profile | `/apple-macbook-pro` req/s | `/electronics` req/s | `/search?q=book` req/s |
|---|---:|---:|---:|
| Default | 18.72 | 32.82 | 19.90 |
| `ForceMinWorkerThreads=2` | 22.10 | 33.26 | 20.21 |
| `UnfairSemaphoreSpinLimit=0` | 16.16 | 29.73 | 18.40 |

`DOTNET_gcServer=1` was also tested but showed no measurable difference for this workload on a 2-vCPU VM (server GC has more impact at higher core counts).

For this workload on Cobalt 100, `DOTNET_ThreadPool_ForceMinWorkerThreads=2` was the most consistent improvement. Disabling spin-waits regressed all three endpoints.

## Why spin-wait can differ on Arm

x86 uses stronger memory ordering, so visibility between cores is often cheaper for spin-heavy synchronization paths. Arm uses a weaker memory model, so those paths can require additional ordering and visibility work when threads move across cores. That makes spin behavior more workload-dependent, so validate with endpoint measurements instead of assuming one fixed spin setting.

## Cobalt generation note

Thread pool spin behavior can vary between Arm processor generations. Re-validate tuning results on the exact generation you deploy to production.

## Architecture-conditional deployment examples

Docker Compose:

```yaml
services:
  nop:
    image: nop-web:publish-test
    environment:
      DOTNET_ThreadPool_ForceMinWorkerThreads: "2"
    deploy:
      placement:
        constraints:
          - node.labels.arch == arm
```

Kubernetes:

```yaml
spec:
  nodeSelector:
    kubernetes.io/arch: arm64
  containers:
    - name: nop
      image: nop-web:publish-test
      env:
        - name: DOTNET_ThreadPool_ForceMinWorkerThreads
          value: "2"
```

## Arm MCP accelerator (optional)

Use MCP to automate repeated benchmark loops and summarize deltas, but keep manual before/after evidence for release decisions.
