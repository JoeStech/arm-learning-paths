---
title: Replace platform-specific code
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

Use one decision framework for every architecture-sensitive dependency.

## Manual workflow (required)

### 1. Find references

```bash
rg -n "System\.Drawing|ColorTranslator" src
```

### 2. Decide using the 4-option framework

1. Keep it and test it deeply.
2. Replace it with a managed cross-platform alternative.
3. Replace it with a native equivalent that has feature parity.
4. Remove the feature if business value is low.

The workflow is the same for every dependency.

### 3. Apply to nopCommerce

- `System.Drawing` image paths: use option 2 with ImageSharp.
- `iTextSharp` PDF color usage: start with option 1 (keep and test), then evaluate option 2 or 3 if you need full decoupling.

```bash
dotnet add src/Libraries/Nop.Services/Nop.Services.csproj package SixLabors.ImageSharp
```

## Baseline: exercise `System.Drawing.Common` first

Before replacing code, run a direct image-operation baseline that uses `System.Drawing.Common` and `ImageSharp` on both x86 and Arm.

Validated Azure results (150 resize+encode operations each) showed:

- `System.Drawing.Common` lower average latency in this microbenchmark.
- `ImageSharp` higher average latency in this microbenchmark.

| Architecture | System.Drawing.Common avg (ms) | ImageSharp avg (ms) |
|---|---:|---:|
| x86 | 15.528 | 32.329 |
| Arm | 15.941 | 70.512 |

This does not invalidate the migration recommendation. Performance is only one dimension.

## Why ImageSharp still improves the migration

A second validated test removed `libgdiplus` on each VM:

- With `libgdiplus` installed: both libraries succeeded.
- Without `libgdiplus`: `System.Drawing.Common` failed (`TypeInitializationException`), ImageSharp still succeeded.

`System.Drawing.Common` depends on `libgdiplus`, a native library that can break portability and behave inconsistently across platforms. ImageSharp is fully managed, so it runs identically on x86 and Arm Linux without native dependencies. For this migration, portability and predictable runtime behavior are the primary goals; then tune and benchmark ImageSharp paths as needed.

### 4. Handle cascade dependencies

Do not patch only the top-level project. If a transitive dependency is architecture-sensitive, fix it first, then retest consumers up the chain.

## Arm MCP accelerator (optional)

Use MCP to accelerate candidate discovery and compatibility research, then validate with manual tests:

- `knowledge_base_search` for replacement candidates
- `migrate_ease_scan` where supported

## Upstream contribution path

When you fix architecture issues in shared code, upstream the change.

- Open a PR with Arm/x86 validation evidence.
- Include benchmark and compatibility notes.
- Track maintainer feedback and update migration docs with the final accepted approach.

This has high leverage: each upstream fix reduces work for future migrations.
