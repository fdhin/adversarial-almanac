---
name: "openvas"
categories:
  - blackarch-scanner
version: "20250606"
homepage: "https://www.openvas.org/"
packaged-by: [blackarch]
blackarch-builddate: "2025-06-06"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "greenbone/openvas-scanner"
github-stars: 4933
github-pushed: "2026-11-09"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 954
gh-activity-checked: "2026-11-09"
---

# openvas

> The Greenbone vulnerability-scanning stack: scan engine, feed handling and management components in one install.

## What it's for

OpenVAS (Open Vulnerability Assessment Scanner) is the scan engine of the Greenbone Community Edition. It executes a continuously updated feed of Vulnerability Tests (VTs, written in NASL) against targets and reports findings. The `openvas` package is a meta package that pulls in the complete stack — the `openvas-scanner` engine, `ospd-openvas` (the OSP daemon that drives it), `gvmd` (management), `gsad`/`gsa` (web interface), `gvm-libs`, `pg-gvm` and `greenbone-feed-sync`. Scans are normally launched through the Greenbone web UI or `gvm-tools` over GMP/OSP; the `openvas` binary itself is the engine daemon and VT-utility front end, not an interactive console scanner.

## Typical use cases

### Check the installed scanner version and configuration

Confirm which engine a host runs and what configuration it parsed.

```
openvas -V
openvas -s
```

Outcome: `-V` prints the scanner version banner and `-s` the parsed configuration settings (with `-y` showing the compile-time config directory) — the first check when the daemon refuses to start or loads the wrong feed.

### Load VT metadata into the Redis store

Make freshly synced Vulnerability Tests known to the scanner.

```
openvas -u
```

Outcome: reads the VT files from the feed directory and updates their metadata into the Redis store used by ospd-openvas; run it after feed content changes, otherwise scans run against stale test info.

### Control a scan by ID from a script

Start or stop a scan whose data was previously staged in the Redis store.

```
openvas --scan-start <scan-id>
openvas --scan-stop <scan-id>
```

Outcome: the engine starts (or aborts) executing the VTs for the given scan ID. Normally invoked by ospd-openvas/gvmd rather than by hand, but usable directly for scripted engine testing.

### Run a single NASL test against a target

Execute one NASL script directly, without the management stack.

```
nasl -t 198.51.100.10 check_12345.nasl
```

Outcome: the `nasl` interpreter shipped with openvas-scanner runs the script against the target and prints its findings; `-p` only parses the script and `-L` lints it without executing.

No corpus evidence — derived from upstream documentation (greenbone/openvas-scanner README, INSTALL.md and the option table in src/openvas.c).
