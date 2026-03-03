---
title: Containerize the application
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

Use a manual-first container workflow, then add MCP checks.

## Manual workflow (required)

### 1. Dockerfile path

```bash
docker build -f Dockerfile -t nop-web:dockerfile-test .
```

The nopCommerce Dockerfile uses `FROM --platform=$BUILDPLATFORM`, which is a BuildKit feature. The legacy Docker builder does not inject `$BUILDPLATFORM`, causing:

```
failed to parse platform : "" is an invalid OS component of ""
```

Fix by switching to buildx:

```bash
docker buildx create --use --name nopx || true
docker buildx build --platform linux/amd64,linux/arm64 -t nop-web:multiarch --push .
```

If you cannot use buildx, create a simplified Dockerfile that removes the `--platform=$BUILDPLATFORM` argument and builds for the host architecture only.

### 2. .NET SDK publish path (single architecture)

```bash
dotnet publish src/Presentation/Nop.Web/Nop.Web.csproj \
  -c Release \
  /t:PublishContainer \
  -p:ContainerImageTag=publish-test
```

Validated outputs in this project:

- x86 image: `591 MB`
- Arm image: `623 MB`

### 3. .NET SDK multi-arch path

Define multi-arch runtime identifiers in the project file, then publish once.

```xml
<PropertyGroup>
  <ContainerRuntimeIdentifiers>linux-x64;linux-arm64</ContainerRuntimeIdentifiers>
</PropertyGroup>
```

```bash
dotnet publish src/Presentation/Nop.Web/Nop.Web.csproj -c Release /t:PublishContainer
```

This is the scalable path for one image definition across both architectures.

## Arm MCP accelerator (optional)

Use MCP image tools after manual build succeeds:

- `skopeo` to inspect manifest architecture coverage
- `check_image` for a quick architecture report

## Recommendation

Use SDK publish as the default migration path. Keep Dockerfile for advanced layer control or custom build logic.
