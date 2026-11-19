---
name: "netexec-pingcastle"
categories:
  - blackarch-windows
version: "9.16340d2"
homepage: "https://github.com/TRIKKSS/CrackMapExec-PingCastle"
packaged-by: [blackarch]
blackarch-builddate: "2024-05-05"
build-system: windows
license: custom:unknown
aliases: ["crackmapexec-pingcastle"]
meta-checked: "2026-11-09"
github-repo: "TRIKKSS/CrackMapExec-PingCastle"
github-stars: 7
github-pushed: "2024-04-18"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netexec-pingcastle

> NetExec/CrackMapExec module that runs a PingCastle Active Directory health check on a remote Windows machine.

## What it's for

PingCastle assesses an Active Directory domain's security posture, but normally you have to run its executable on a Windows host yourself. This module wraps that step into NetExec (`nxc`) / CrackMapExec (`cme`): with valid admin credentials over SMB, one command executes your PingCastle binary on each remote machine and surfaces the results in the NetExec output — practical for sweeping many hosts from a single Linux box. The module does no analysis itself; it drives an existing PingCastle executable supplied via `PINGC_PATH`, with optional config file and flags.

## Typical use cases

### Run PingCastle on a remote host

Authenticate over SMB and trigger the health check on the target.

```
nxc smb 198.51.100.10 -d example.com -u user -p Password1 -M pingcastle -o PINGC_PATH=/opt/PingCastle/PingCastle.exe
```

Outcome: NetExec authenticates, runs PingCastle on the remote machine using the binary at `PINGC_PATH`, and the health-check output appears inline in the NetExec result for that host.

### List the module's options

Check the parameters the module accepts before a run.

```
nxc smb -M pingcastle --options
```

Outcome: prints the module options — `PINGC_PATH` (path to the PingCastle executable), `PINGC_CONF` (optional config file) and `PINGC_FLAG` (optional flags passed to PingCastle).

### Pass custom PingCastle flags and a config

Run a specific health check with non-default settings on the remote host.

```
nxc smb 198.51.100.10 -d example.com -u user -p Password1 -M pingcastle -o PINGC_PATH=/opt/PingCastle/PingCastle.exe PINGC_CONF=/opt/PingCastle/PingCastle.exe.config PINGC_FLAG='--no-enum-limit,--healthcheck'
```

Outcome: PingCastle runs on the target with the supplied config and the `--healthcheck` / `--no-enum-limit` flags; multiple flags are comma-separated inside `PINGC_FLAG`.

### Drive the same module through CrackMapExec

The module also installs for the older CrackMapExec entry point.

```
cme smb 198.51.100.10 -d example.com -u user -p Password1 -M pingcastle -o PINGC_PATH=/opt/PingCastle/PingCastle.exe
```

Outcome: identical behavior through `cme`, so existing CrackMapExec workflows can run the module unchanged.

*No corpus evidence — derived from upstream documentation (TRIKKSS/CrackMapExec-PingCastle README).*
