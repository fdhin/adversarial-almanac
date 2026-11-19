---
name: "wmi-client"
categories:
  - windows
version: "1.3.14"
homepage: "https://bitbucket.org/instarch/wmi-client/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "imvishalpatel/wmi-1.3.14"
github-stars: 0
github-pushed: "2020-01-17"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wmi-client

> `wmic` — Linux command-line client for remote WMI queries against Windows hosts.

## What it's for

wmi-client ships `wmic`, the Samba-derived Linux CLI for issuing WMI (Windows Management Instrumentation) queries to remote Windows machines over DCOM: authenticate with domain credentials, run a WQL `SELECT` against a namespace (default `root\cimv2`), and get rows back on stdout. It is the lightweight scripted alternative to PowerShell remoting for pulling host inventory — OS, patches, processes, services, antivirus state — from Linux during assessments, and it needs only outbound DCOM (TCP/135 plus a dynamic port) to the target. The packaged code is the long-lived community fork of the original Samba-era wmic. It only queries WMI; it is not a general remote-execution tool.

## Typical use cases

### Query a host's operating system

The canonical smoke test that credentials and DCOM work.

```
wmic -U user%Password1 //198.51.100.10 "select * from Win32_ComputerSystem"
```

Outcome: authenticates over WMI and prints the WQL result rows (manufacturer, model, logged-on user for this class); a credential or DCOM failure returns an NTLM/RPC error instead.

### Query with a domain account and a different namespace

Pull data that lives outside the default namespace, authenticated as a domain user.

```
wmic -U CORP/user%Password1 //198.51.100.10 --namespace=root\subscription "select * from __Namespace"
```

Outcome: lists the namespaces under `root\subscription` as the domain account; the same pattern with any WQL class (e.g. `Win32_QuickFixEngineering` for patches) dumps that inventory to stdout for parsing, and `--delimiter=` changes the separator used when a property returns multiple values.

GitHub: third-party mirror of the wmi-client 1.3.14 C source (imvishalpatel/wmi-1.3.14).

*No corpus evidence — derived from upstream documentation and verified by executing a `wmic` binary from the same fork lineage (`--help` output: `Usage: //host query`, options `-U`, `--namespace`, `--delimiter`).*
