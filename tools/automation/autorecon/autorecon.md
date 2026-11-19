---
name: "autorecon"
categories:
  - blackarch-automation
version: "297.e7e98f6"
homepage: "https://github.com/Tib3rius/AutoRecon"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Tib3rius/AutoRecon"
github-stars: 6143
github-pushed: "2026-11-03"
github-archived: false
github-issues: 43
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# autorecon

> Multi-threaded reconnaissance launcher that port-scans targets, then automatically fans out service-specific enumeration tools and files everything into a per-target results tree.

## What it's for

AutoRecon (Tib3rius) automates the first hours of a network assessment: it runs port scans and service detection, and for every service it identifies it launches a configurable set of enumeration plugins — directory busting with feroxbuster on HTTP, SMB/RPC enumeration, SNMP walks, SSL scanning, whatweb/nikto, and more. It accepts multiple concurrent targets (IPs, CIDR ranges, resolvable hostnames, IPv6), writes results into `results/<target>/` with `scans/`, `exploit/`, `loot/`, and `report/` subdirectories, and logs every command it ran (`_commands.log`) plus the follow-up commands it deliberately did not run automatically (`_manual_commands.txt`). The default configuration performs no exploitation — it is an enumeration orchestrator, and the plugin set (selected by tags), wordlists, and timeouts are all configurable via `~/.config/AutoRecon/config.toml`, command-line options, and plugin arguments. Scans that need SYN/UDP scanning must run under sudo (e.g. `sudo $(which autorecon) ...`).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Scan a single target verbosely

The standard first move against one host.

```
autorecon 198.51.100.10 -v
```

Outcome: port scans run, then per-service enumeration plugins launch (announced with `-v`, along with open ports and identified services); everything lands under `./results/198.51.100.10/` — scan output in `scans/` per port (e.g. `scans/tcp80/`), manual follow-ups in `scans/_manual_commands.txt`.

### Sweep a target list into a named results directory

Process a whole scope concurrently and keep results organized per engagement.

```
autorecon -t targets.txt -o engagement-results --heartbeat 30
```

Outcome: every target in `targets.txt` is scanned in parallel (concurrency capped by `-m`, default 50) with a status heartbeat every 30 seconds, and each target gets its own subdirectory under `engagement-results/`.

### Restrict ports and scan intensity

Scan only the ports you care about with bounded concurrency.

```
autorecon 198.51.100.10 -p T:21-25,80,443 -m 20
```

Outcome: port scanning is limited to those TCP ports (prepend `U:` for UDP, `B:` for both) with at most 20 concurrent scans, so the subsequent service enumeration only covers those ports.

### Skip port scanning entirely

Jump straight to service enumeration when open services are already known.

```
autorecon 198.51.100.10 --force-services tcp/80/http tcp/443/https/secure
```

Outcome: AutoRecon skips the port-scan phase and treats the listed services as present, immediately launching the HTTP/HTTPS enumeration plugins against them.

### Scan one target without the per-target directory

Keep a single-target engagement tidy.

```
autorecon --single-target 198.51.100.10 -o app-results
```

Outcome: the `scans/`, `exploit/`, `loot/`, and `report/` structure is created directly inside `app-results/` rather than under a target-named subdirectory.

### Inspect and select the plugin set

See what will run before pointing it at a scope.

```
autorecon --list service
```

Outcome: prints all service-scan plugins (`--list port` and `--list report` cover the other types); at scan time, `--tags`/`--exclude-tags` control which plugins actually execute, e.g. `--dirbuster.tool gobuster` switches the directory-busting plugin's tool.
