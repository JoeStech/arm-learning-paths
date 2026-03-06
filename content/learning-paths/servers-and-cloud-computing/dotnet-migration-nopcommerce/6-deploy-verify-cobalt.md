---
title: Performance tuning on Cobalt
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Performance tuning on Cobalt

Treat tuning as an experiment pipeline, not a one-shot tweak. On Arm, apply changes in a controlled sequence, measure with fixed workloads, and keep only optimizations that repeatedly improve your target metrics.

## Tuned run configuration

Start with a conservative runtime profile that is commonly effective for server workloads.

```bash
export DOTNET_TieredPGO=1
export DOTNET_ReadyToRun=1
export DOTNET_gcServer=1
export DOTNET_gcConcurrent=1
export DOTNET_EnableDiagnostics=0
export DOTNET_ThreadPool_ForceMinWorkerThreads=2
```

Run the same endpoint suite used in the baseline:

```bash
# Keep benchmark parameters identical to baseline for valid comparison.
python3 test_nopcommerce_endpoints.py \
  --base-url http://127.0.0.1:5000 \
  --concurrency 8 \
  --iterations 20 \
  --json-out arm_after.json
```

## Validation rules for credible tuning gains

Use these rules before adopting a tuning profile:

- Run at least 5 baseline and 5 tuned trials.
- Keep endpoint sequence fixed across all runs.
- Reset warm-up policy consistently (always warm or always cold).
- Require improvement in both throughput and p95 latency, not just one.
- Set a minimum practical threshold (for example, >=5% median gain) before rollout.

## Interpretation

- If only one metric improves while p95 or error rate regresses, do not treat the change as a win.
- If run-to-run variation is near the observed delta, treat it as noise.
- Keep architecture-specific profiles separate; do not force one profile onto all architectures.


Use this page as a tuning workflow template, then validate with your production-like traffic profile before rollout.
