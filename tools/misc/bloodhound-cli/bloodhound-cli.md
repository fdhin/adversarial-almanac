---
name: "bloodhound-cli"
categories:
  - blackarch-misc
version: "v0.2.1.r2.gef78c50"
homepage: "https://github.com/SpecterOps/bloodhound-cli"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-13"
build-system: go
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "SpecterOps/bloodhound-cli"
github-stars: 128
github-pushed: "2026-07-03"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# bloodhound-cli

> Install, update and operate a BloodHound Community Edition deployment from the shell.

*No corpus evidence — derived from upstream documentation.*

## What it's for

`bloodhound-cli` is the Go binary from SpecterOps' BloodHound project that manages a BloodHound Community Edition (CE) installation. It wraps Docker Compose (or Podman in Docker-compatibility mode) to download container images, generate the `docker-compose.yml` with standard defaults, bootstrap the administrator account, and control the CE stack (database, application and UI tiers). It is the operational tool for the BloodHound host — it does not collect data. Collection is done by SharpHound (Active Directory), AzureHound (Entra ID/Azure — see [[azurehound]]) or OpenHound collectors, whose ZIP/JSON output you then ingest through the CE UI or API. It replaces the old "run docker-compose yourself" workflow with one supported command surface.

## Typical use cases

### Install BloodHound CE

```
bloodhound-cli install
```

Outcome: pulls the required container images, writes a default `docker-compose.yml` (bound to 127.0.0.1 only) and starts the stack; at the end it prints a randomly generated admin password. Log in at http://localhost:8080/ui/login as `admin` with that password and reset it when prompted.

### Update an existing deployment

```
bloodhound-cli update
```

Outcome: pulls the newest BloodHound images and brings the running CE stack up to date — the recommended upgrade path for an installed instance.

### Discover the full command set

```
bloodhound-cli help
```

Outcome: prints usage and every available subcommand for controlling and configuring the Docker Compose-backed deployment.
