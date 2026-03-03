---
title: Deploy and verify on Cobalt
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

Deploy on Arm Cobalt, verify correctness, and record architecture-specific behavior.

## Manual workflow (required)

### 1. Run the container

```bash
docker rm -f nop-web || true
docker run -d --name nop-web -p 5000:5000 nop-web:publish-test
```

### 2. Verify health and architecture

```bash
curl -sS -o /dev/null -w '%{http_code}\n' http://127.0.0.1:5000/install
uname -m
```

### 3. Validate core user flows

Verify at least home, search, product, cart, and checkout.

Use a product URL that exists in your seeded catalog (for example, `/apple-macbook-pro`). In this path's validation runs, `/checkout` returned `302` when no authenticated checkout session existed.

## Cobalt generation note

This path was validated on Cobalt 100. Thread pool spin behavior and tuning results can vary between processor generations, so re-validate tuning settings if you deploy on a different generation (e.g., Cobalt 200).

## Arm MCP accelerator (optional)

Use MCP tools for faster deployment checks and architecture inspection, but keep manual functional checks as the release gate.

## Production checklist

- Persistence across restart
- Service dependency connectivity
- Functional flow completeness
- Architecture-specific configuration correctness
