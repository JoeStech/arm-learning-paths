---
title: Migrate nopCommerce to Azure Cobalt 100 (Arm)

minutes_to_complete: 75

who_is_this_for: This learning path is for .NET and platform engineers migrating a production .NET application from x86 to Arm on Azure.

learning_objectives:
    - Build a pinned nopCommerce release on both x86 and Arm with the same .NET toolchain
    - Use a manual-first dependency workflow, then accelerate it with the Arm MCP server
    - Produce an SBOM and resolve platform-specific dependency cascades
    - Containerize with Dockerfile and .NET SDK multi-arch publish workflows
    - Apply architecture-conditional runtime tuning with measured results

prerequisites:
    - Azure account with permissions to create VMs
    - Azure CLI (`az`) installed locally
    - Docker and .NET 9 SDK familiarity
    - Basic Linux command-line knowledge

author: Joe Stech

### Tags
skilllevels: Advanced
subjects: Cloud Migration and Performance
armips:
    - Neoverse
tools_software_languages:
    - .NET
    - C#
    - Docker
    - Azure CLI
    - PostgreSQL
operatingsystems:
    - Linux

further_reading:
    - resource:
        title: nopCommerce repository
        link: https://github.com/nopSolutions/nopCommerce
        type: documentation
    - resource:
        title: .NET SDK container publish overview
        link: https://learn.microsoft.com/dotnet/core/containers/overview
        type: documentation
    - resource:
        title: .NET on Arm
        link: https://learn.microsoft.com/dotnet/core/install/linux-arm64
        type: documentation
    - resource:
        title: SixLabors ImageSharp
        link: https://docs.sixlabors.com/
        type: documentation

### FIXED, DO NOT MODIFY
# ================================================================================
weight: 1                       # _index.md always has weight of 1 to order correctly
layout: "learningpathall"       # All files under learning paths have this same wrapper
learning_path_main_page: "yes"  # This should be surfaced when looking for related content. Only set for _index.md of learning path content.
---

This path uses a consistent pattern in every module:

1. Do it manually so the workflow is clear.
2. Use the Arm MCP server as an optional accelerator.

All commands and results were validated on 2026-02-25 with nopCommerce `release-4.90.3` (`9beda11c42`) and .NET SDK `9.0.114`.

## Why this matters

- It helps teams move .NET workloads to Cobalt with fewer migration regressions.
- It closes the gap between generic modernization guidance and architecture-specific execution.
- It gives AI-assisted migration a measurable validation loop instead of guesswork.
- It encourages upstream fixes so future migrations are easier for everyone.

## What you will do

1. Set up comparable x86 and Arm environments on Azure.
2. Build and baseline with a workload that goes beyond `/install`.
3. Audit direct and transitive dependencies with an SBOM-first approach.
4. Apply a reusable 4-option decision framework to platform-specific dependencies.
5. Containerize with Dockerfile and .NET SDK multi-arch techniques.
6. Deploy on Cobalt, validate functionality, and tune with architecture-aware settings.
