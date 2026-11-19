---
name: "vanguard"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://packetstormsecurity.com/files/110603/Vanguard-Pentesting-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# vanguard

> Module-driven Perl web-application scanner that spiders a host and runs recon and test plug-ins (XSS, SQLi, LFI, RCE, shell detection, nmap bridge).

## What it's for

Vanguard (the "Vanguard Pentesting Scanner", 2011, distributed via Packet Storm) is a comprehensive Perl web-penetration tool built around a module chain: API and recon modules load first (crawler, webapp detection, shell detection, an nmap bridge), followed by test modules — XSS_GET, SQL_GET, LFI_GET and RCI_GET in the shipped whitelist. Pointed at a host it walks that chain and reports findings. Modules are enabled or whitelisted through a config.yml file, results can be written to an output file, and an IDS-evasion level can be selected for generated requests. It must run as root (it exits with an error otherwise), and it is a 2011-era Perl 5 codebase: expect dated dependencies and module behavior. Note the unusual flag: -h takes the HOST, not help.

## Typical use cases

### Run a full module scan against a host

The standard run.

```
sudo vanguard -h target.example.com
```

Outcome: prints "Loading modules" with the module list from the whitelist (CRAWL, WEBAPPS, SHELL, NMAP recon first, then the XSS/SQL/LFI/RCI test modules), scans the host, and streams findings to the console. Runs as root only.

### Save results to a file with verbose output

Keep a report.

```
sudo vanguard -h target.example.com -o vanguard-report.txt -v
```

Outcome: same scan, but results are additionally written to vanguard-report.txt and verbose per-module detail is printed.

### Tune evasion and the module set

Adapt to a defended target.

```
sudo vanguard -h target.example.com -e 1 -c /path/to/config.yml
```

Outcome: enables an IDS-evasion technique for generated requests (-e) and loads an alternate module-whitelist configuration from the given path (-c) instead of the packaged default config.yml.

No corpus evidence — derived from the packaged upstream source (scan.pl usage and config.yml of vanguard-public 0.1, Packet Storm).
