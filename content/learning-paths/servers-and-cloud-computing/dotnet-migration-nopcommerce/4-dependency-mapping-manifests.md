---
title: Audit dependencies
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

Use a manual-first audit, then use the Arm MCP server to accelerate checks.

## Manual workflow (required)

### 1. Map direct references

```bash
rg -n "<PackageReference|<ProjectReference" src
```

### 2. List transitive dependencies

```bash
dotnet list src/Presentation/Nop.Web/Nop.Web.csproj package --include-transitive
```

### 3. Generate an SBOM (required)

```bash
dotnet tool install --global CycloneDX
dotnet CycloneDX src/Presentation/Nop.Web/Nop.Web.csproj -o sbom/
```

If tool installation is blocked, treat `dotnet list --include-transitive` plus `*.deps.json` evidence as a temporary fallback, not the final state.

### 4. Inspect package internals for native payloads

```bash
mkdir -p /tmp/nupkg-audit
cp ~/.nuget/packages/<package>/<version>/<package>.<version>.nupkg /tmp/nupkg-audit/
cd /tmp/nupkg-audit
unzip -l <package>.<version>.nupkg | rg "runtimes/|native/"
```

This is how you catch hidden architecture-specific binaries.

## Dependency cascade rule

Treat dependencies as a chain, not isolated items:

- App depends on library A
- Library A depends on library B
- Library B is architecture-sensitive

You must resolve B first, then validate A, then validate the app. In nopCommerce, `iTextSharp` and `System.Drawing` are an example of this chain.

## Arm MCP accelerator (optional)

After manual evidence is collected, use MCP tools to speed repetitive checks:

- `knowledge_base_search` for package compatibility
- `skopeo` or `check_image` for container image architecture coverage
- `migrate_ease_scan` where scanner support exists

Keep manual outputs as the source of truth. Use MCP to prioritize and scale, not to skip evidence.
