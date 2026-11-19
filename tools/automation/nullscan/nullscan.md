---
name: "nullscan"
categories:
  - blackarch-automation
version: "1.0.1"
homepage: "http://www.nullsecurity.net/tools/automation.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "noptrix/nullscan"
github-stars: 44
github-pushed: "2021-01-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nullscan

> Modular framework that chains and automates arbitrary security tools against nmap-discovered or directly-specified targets.

## What it's for

nullscan (noptrix/nullsecurity) is a pentest automation harness: it takes target definitions from the command line — hosts to nmap-scan first, URIs to attack directly, or an existing nmap XML log — then dispatches its modules (groups of external tools like hydra, sqlmap, wpscan, crack-* and many more) against whatever ports/services were found, with configurable parallel workers, per-tool timeouts, and include/exclude filters. Results are written into a structured per-run directory and can be rendered into an HTML report. It wraps and schedules tools you already have installed; it is only as capable as the binaries on your PATH, so run its dependency check before relying on it.

## Typical use cases

### Port-scan a subnet, then attack the services found

Flagship one-shot: nmap first, filtered modules, HTML report.

```
nullscan -t 198.51.100.0/24 -i tcp=ssh,http -r -I hydra_ssh,crack_http_auth
```

Outcome: nullscan runs nmap over the subnet, keeps only hosts with SSH or HTTP open, then executes just the `hydra_ssh` and `crack_http_auth` tools (`-I` includes specific tools; `-i` includes module groups) against them in parallel. A dated work/log directory is created in the current directory, and `-r` adds a generated HTML report alongside the per-tool logs.

### Attack explicit URI targets directly

Skip discovery when you already know the endpoints.

```
nullscan -u 'tcp://target.example.com:80=http,22=ssh;http://www.example.com' -o 'user=root;plists=/tmp/pwds.txt'
```

Outcome: parses the URI string into service/port pairs per host and runs the matching modules against them without any nmap phase; `-o` feeds options through to the modules — here a username and a password list shared by the brute-forcing tools. `-T`/`-M`/`-P` tune target, module, and tool worker counts for bigger runs.

### Audit your toolchain before a run

Check which of the wrapped tools are actually installed.

```
nullscan -C
nullscan -p 'tcp=ssh,http;udp'
```

Outcome: `-C` verifies every tool referenced by the enabled modules and reports what is missing on this host (recommended before a long unattended run); `-p` just prints which tools a module selection would use, per protocol group, without executing anything. `-V` prints the nullscan version and `-H` the full help including the `?`-suffix trick (`-i ?`, `-m ?`) for per-option examples.

### Extend the framework with your own module

Register a new tool permanently.

```
nullscan -m 'icmp/ping ping_flood ping -f -s 9999'
nullscan -a 'tcp/ssh crack_ssh sshcracker -c arg -f arg'
```

Outcome: `-m` creates a new module (`icmp/ping`) whose `ping_flood` tool invokes `ping` with those arguments, and `-a` adds the `sshcracker` tool to the existing `tcp/ssh` module's `crack_ssh` entry; both become selectable via `-i`/`-I` in subsequent runs.

*No corpus evidence — derived from upstream documentation.*
