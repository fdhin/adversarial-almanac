---
name: "sn00p"
categories:
  - blackarch-automation
version: "0.8"
homepage: "http://www.nullsecurity.net/tools/automation.html"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sn00p

> Bourne-shell framework that chains existing security tools against parsed targets.

## What it's for

sn00p (nullsecurity, noptrix) orchestrates other security tools: you feed
it target definitions — hosts and ports on the command line, a host file,
URLs, or an existing nmap XML log — and it runs the matching modules and
their third-party tools, logs every result in a per-target directory
structure and can generate an HTML or text report from the collected
output. It stayed beta (0.8) and was later superseded by the same author's
nullscan. It is pure orchestration: nearly every actual check is delegated
to the tools it drives, so its value is the chaining, logging and
reporting.

## Typical use cases

### Run modules against command-line targets

```
sn00p -h 'tcp://target.example.com:80=http,21=ftp;udp://target.example.com:123=ntp'
```

Outcome: parses the host/port/service definitions, runs the modules mapped
to those services (all modules by default, `-i`/`-x` to include/exclude)
and stores each tool's output under its log directory for that target.

### Replay an existing nmap XML scan

```
sn00p -f /tmp/scanned.xml -i 'host=icmp;tcp=default' -r html
```

Outcome: reads the open TCP/UDP ports straight from the nmap XML log, runs
only the included modules (here host-level checks plus the default TCP
set) and writes an HTML report from the results.

### Scan a host file while excluding noisy tools

```
sn00p -l hosts.txt -X nikto,wpscan -v -o 'nmap=-sT,-n,-Pn.-v'
```

Outcome: nmap-scans the hosts listed in `hosts.txt`, skips the excluded
tools inside every module, and passes the extra options in `-o` through to
the underlying nmap invocation.

### Verify third-party tool availability before a run

```
sn00p -C
```

Outcome: checks every external tool sn00p can drive for presence on this
host and reports what is missing, so a long chained run does not die
halfway through for want of a dependency.

*No corpus evidence — derived from upstream documentation.*
