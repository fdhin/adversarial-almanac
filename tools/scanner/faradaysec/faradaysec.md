---
name: "faradaysec"
categories:
  - blackarch-scanner
version: "v3.19.0.r2583.g77edf29"
homepage: "http://www.faradaysec.com/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-31"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# faradaysec

> Faraday, the open-source vulnerability management platform by Infobyte: a multiuser server that ingests scanner output, normalizes it into workspaces, and serves a web UI for tracking findings to remediation.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Faraday attacks the second half of pentest work: keeping track of findings. Instead of a pile of per-tool output files, Faraday (built by Infobyte from the `infobyte/faraday` repository, which is what the BlackArch package compiles) runs a server that aggregates and normalizes results loaded through its plugins, deduplicates hosts and vulnerabilities across scanners, and exposes everything as workspaces, dashboards and reports that managers and analysts can share in real time. The server piece (`faraday-server`, plus the `faraday-manage` administration entry point) is the core; a separate terminal client (`faraday-cli`) exists for driving workspaces from scripts or CI pipelines. Faraday is not itself a scanner — it is where scanner results go to be managed.

## Typical use cases

### Initialize the database before first run

```
faraday-manage initdb
```

Outcome: creates or upgrades the PostgreSQL schema Faraday stores workspaces in; this is the first command of the upstream quick-start sequence and needs to succeed before the server will start on a fresh install.

### Start the server and work in the web UI

```
faraday-server
```

Outcome: launches the API and web application listening on port 5985; browse to `http://localhost:5985` and log in with the credentials created during setup to see workspaces, host/vulnerability views and dashboards fed by the tool plugins' parsed output.
