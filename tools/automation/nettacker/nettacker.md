---
name: "nettacker"
categories:
  - blackarch-automation
version: "0.4.1.r17.g14ef2b4"
homepage: "https://github.com/OWASP/Nettacker"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: python
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "OWASP/Nettacker"
github-stars: 5716
github-pushed: "2026-11-06"
github-archived: false
github-issues: 296
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 141
gh-activity-checked: "2026-11-09"
---

# nettacker

> OWASP Nettacker: modular, multithreaded automation for reconnaissance and vulnerability scanning across networks, web apps, and APIs.

## What it's for

Nettacker (formerly Ali-Razmjoo's OWASP-Nettacker, now the OWASP/Nettacker repo) automates information gathering and pentest busywork: port scanning, service detection, subdomain enumeration, directory brute-forcing, vulnerability modules, and credential brute-force, each as a selectable module (`-m`) run in parallel across targets. Targets can be single IPs, ranges, CIDRs, domains, or URLs, mixed freely or loaded from a file; results go to text/CSV/JSON/HTML reports and a local database that supports diffing scans over time (drift detection for CI/CD). It also ships a REST API and web UI. Use it when you want one repeatable command for broad, scripted sweeps instead of hand-chaining nmap/gobuster/hydra per host.

## Typical use cases

### Port-scan a host or subnet

Quick TCP port inventory through one module.

```
nettacker -i 198.51.100.10 -m port_scan
nettacker -i 198.51.100.0/24 -m port_scan -g 22 -t 10
```

Outcome: the `port_scan` module probes each target; with `-g 22` only hosts with that port open are reported, and `-t` raises threads per host. Findings stream to the console with timestamps and are stored for the run's report.

### Enumerate subdomains and their HTTP status

Map a domain's live web presence.

```
nettacker -i example.com -d -s -m http_status_scan
```

Outcome: `-d` enables subdomain enumeration and `-s` port-scans discovered hosts before the `http_status_scan` module queries each web service; output lists each subdomain/service with its HTTP status code.

### Full sweep with credential brute-force and a JSON report

Combine modules, wordlists, and structured output.

```
nettacker -i 198.51.100.0/24 -m all -t 10 -M 35 -g 20-100 -o result.json -u root -P passwords.txt
```

Outcome: runs every enabled modules in parallel (`-t` threads per host, `-M` parallel modules) against ports 20-100, trying user `root` with each password in `passwords.txt` where a module supports login; all findings are written to `result.json` (`-o` also accepts `.html`, `.csv`, `.txt` — add `--graph d3_tree_v2_graph` for an interactive HTML graph). Exclude noisy modules with `-x ssh_br`, or swap user/password lists with `-U users.txt -p pass123`.

### List available modules and scan profiles

See what can run before committing.

```
nettacker --show-all-modules
nettacker -i 198.51.100.10 --profile info
```

Outcome: the first prints all 100+ module names (usable with `-m`, including wildcards like `-m *_scan` or `-m *_vuln`); the second runs the curated `info` profile — a preset bundle of information-gathering modules (`--profile all`, `vuln`, `high_severity` also exist) without hand-picking modules.

### Start the API and web UI

Drive scans from a browser instead of the shell.

```
nettacker --start-api --api-port 5000
```

Outcome: Nettacker launches its API service (login key printed to the console/log) and the web UI becomes available on the configured port, where scans can be defined, run, and their results browsed; scan history persists in the local sqlite database.

*No corpus evidence — derived from upstream documentation.*
