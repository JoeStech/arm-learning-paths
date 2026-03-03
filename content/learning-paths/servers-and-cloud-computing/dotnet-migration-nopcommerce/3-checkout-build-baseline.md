---
title: Build and baseline
weight: 3

### FIXED, DO NOT MODIFY
layout: learningpathall
---

Pin nopCommerce to a reproducible release, build on both architectures, and capture a baseline that includes core storefront paths.

## Clone and pin the source

```bash
git clone https://github.com/nopSolutions/nopCommerce.git
cd nopCommerce
git fetch --tags --prune
git checkout release-4.90.3
git rev-parse --short=10 HEAD   # expect 9beda11c42
```

## Restore and build

```bash
dotnet restore src/Presentation/Nop.Web/Nop.Web.csproj
dotnet build src/Presentation/Nop.Web/Nop.Web.csproj -c Release --no-restore
```

## Start the application

```bash
cd src/Presentation/Nop.Web
dotnet run -c Release --no-build --urls http://0.0.0.0:5000
```

## Baseline methodology

Do not rely on `/install` alone. Baseline at least:

- Home page
- Search
- Product page
- Cart
- Checkout

Prerequisite: complete nopCommerce setup first (database configured and installer finished). Otherwise, storefront paths redirect to the install flow and do not represent real behavior.

Use a valid product URL from your seeded catalog (for example, `/apple-macbook-pro`), not a hard-coded slug that may not exist in your dataset.

Quick smoke probe (pre-install, useful as a connectivity check only):

```bash
for p in / \
         '/search?q=book' \
         '/apple-macbook-pro' \
         '/cart' \
         '/checkout'; do
  echo "== $p =="
  for i in $(seq 1 20); do
    curl -sS -o /dev/null -w '%{time_total}\n' "http://127.0.0.1:5000$p"
  done
done
```

Before installation is complete, all storefront paths redirect to setup (`302`). Those numbers confirm reachability but are not valid performance baselines.

## Installed-store endpoint baseline (validated)

After fixing PostgreSQL setup (`citext`) and completing installation on both machines, the same endpoint benchmark was executed with `wrk` (`-t2 -c32 -d20s`) across this set:

- `/`
- `/search?q=book`
- `/electronics`
- `/apple-macbook-pro`
- `/cart`
- `/checkout`

Storefront pages returned `200`; `/checkout` returned `302` (expected without an authenticated session).

Requests/second comparison:

| Endpoint | x86 | Arm | Arm vs x86 |
|---|---:|---:|---:|
| `/apple-macbook-pro` | 35.40 | 32.28 | -8.8% |
| `/electronics` | 99.52 | 76.07 | -23.6% |
| `/search?q=book` | 28.18 | 27.40 | -2.8% |

Different endpoints show different gaps. Avoid reducing migration performance to a single number — use per-endpoint baselines to guide architecture-specific optimizations.

## Arm MCP accelerator (optional)

Use MCP to script repeated baseline runs and summarize deltas, but keep the manual baseline commands as your ground truth.
