---
title: Environment setup
weight: 2

### FIXED, DO NOT MODIFY
layout: learningpathall
---

Create two VMs in the same Azure region with matching OS and tooling so differences are attributable to architecture, not configuration drift.

This path was validated with Ubuntu 24.04 LTS VMs in `westus2`:

| | x86 | Arm |
|---|---|---|
| **VM size** | Standard_D2s_v6 | Standard_D2ps_v6 |
| **vCPUs** | 2 | 2 |

## Log in and create VMs

```bash
az login --use-device-code
az account set --subscription <your-subscription>
```

Create both VMs in the same resource group and region.

## Install tools on each VM

```bash
sudo apt-get update -y
sudo apt-get install -y git curl jq ca-certificates gnupg docker.io
sudo usermod -aG docker "$USER"

sudo add-apt-repository ppa:dotnet/backports -y
sudo apt-get update -y
sudo apt-get install -y dotnet-sdk-9.0
```

Verify:

```bash
uname -m
dotnet --version
docker --version
```

Expected output: `x86_64` on the x86 VM, `aarch64` on the Arm VM. Both should report the same .NET SDK version (9.0.x).

## PostgreSQL prerequisite for nopCommerce install

nopCommerce migrations on PostgreSQL require the `citext` extension. Create it before running the installer:

```bash
docker run -d --name nop-postgres \
  -e POSTGRES_USER=nop \
  -e POSTGRES_PASSWORD=<password> \
  -e POSTGRES_DB=nopcommerce \
  -p 5432:5432 postgres:16

docker exec nop-postgres psql -U nop -d nopcommerce \
  -c "CREATE EXTENSION IF NOT EXISTS citext;"
```

## Optional: script-driven remote execution

```bash
az vm run-command invoke \
  -g <resource-group> \
  -n <vm-name> \
  --command-id RunShellScript \
  --scripts @./your-test-script.sh
```

This was used to run identical commands on both VMs.
