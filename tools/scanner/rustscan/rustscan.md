---
name: "rustscan"
categories:
  - scanner
homepage: "https://github.com/RustScan/RustScan"
origin: "pentest-book"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: rust
github-repo: "RustScan/RustScan"
github-stars: 20714
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "GPL-3.0"
---

# rustscan

> Fast asynchronous port scanner that finds all 65k ports in seconds and hands them straight to nmap.

## What it's for

RustScan is a modern port scanner built for speed: it probes all 65,535 ports in seconds (3,000 ports per second by default) and then automatically pipes the open ports into nmap, so one command gives you rustscan's breadth plus nmap's service detection. The default speed is loud — it can trip IDS/IPS or get your IP blocked — so batch size and per-connection timeout flags throttle it down when stealth matters. A scripting engine (Python, Lua, shell) can react to discovered ports, for example auto-running SMB enumeration when port 445 answers. Hostnames, IPv6, CIDR blocks, and newline-separated target files are all accepted. It is not a service/version detector by itself: that part is done by the nmap it invokes.

## Typical use cases

### Full-port scan with automatic nmap handoff

One command for all 65k ports plus service enumeration.

```
rustscan -a 198.51.100.10
```

Outcome: all ports are probed, open ones print as `Open 198.51.100.10:443` lines, then nmap runs automatically (roughly `nmap -Pn -vvv -p $PORTS 198.51.100.10`) against exactly the ports found.

### Check a specific set of ports

Fast verification of known or suspected service ports.

```
rustscan -a 198.51.100.10 -p 22,80,443
```

Outcome: only the listed ports are probed; each open port is reported and passed to the nmap stage.

### Scan a range in randomized order

Cover the low ports without an obvious sequential pattern.

```
rustscan -a 198.51.100.10 --range 1-1000 --scan-order Random
```

Outcome: ports 1-1000 are probed in random order (harder for pattern-matching defenses to fingerprint) and open ports are listed as normal.

### Throttle the scan for stealth

Slow down so fragile or watched targets survive the attention.

```
rustscan -a 198.51.100.10 -b 10 -T 5000
```

Outcome: ports are probed in batches of 10 with up to 5 seconds' wait per connection — much slower than the 3,000-ports/second default but far less likely to trigger rate-based defenses or crash a sensitive server.

### Control the nmap stage directly

Add version detection or scripts to the automatic follow-up scan.

```
rustscan -a 198.51.100.10 -- -sV -sC
```

Outcome: everything after `--` is passed verbatim to nmap, which runs its default invocation plus your flags (here service version detection and default scripts) on the ports rustscan found.

### Feed targets from a file or CIDR

Scale the same scan across many hosts.

```
rustscan -a 'targets.txt'
```

Outcome: rustscan reads newline-separated targets (IPs, hostnames, or CIDR blocks) from the file and scans each in turn; `-a 198.51.100.0/30` does the same for a range given inline.

No corpus evidence — derived from upstream documentation.
