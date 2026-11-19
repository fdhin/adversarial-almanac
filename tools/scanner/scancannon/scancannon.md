---
name: "scancannon"
categories:
  - scanner
homepage: "https://github.com/johnnyxmas/ScanCannon"
github-repo: "johnnyxmas/ScanCannon"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: CC-BY-NC-3.0
github-stars: 481
github-pushed: "2026-10-06"
github-commits-year: 15
gh-checked: "2026-11-14"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# scancannon

> Bash pipeline that marries masscan's speed with nmap's accuracy — internet-scale enumeration of credential-based attack surfaces, with project folders and scan-to-scan diffing.

## What it's for

ScanCannon enumerates very large external IPv4 ranges efficiently: masscan sweeps a focused port list (SSH, FTP, Telnet, DNS, HTTP/S, SMB, MSRPC, SFTP, L2F, PPTP — the services credentials get typed into) at high rate, then nmap deep-scans only the responsive hosts for versions and detail, compensating for masscan's inaccuracy. Around that core it wraps ASN-based discovery (resolve a domain or CIDR → whois → every prefix the ASN announces via RADB, with an interactive range picker), optional API-endpoint detection (`-a`, nmap NSE fingerprints plus curl probes of `/api`, `/swagger.json`, `/graphql` and friends), CVE hinting via the nmap `vulners` NSE (`-V`), and per-project output with a "what changed since last scan" diff — repeat runs become attack-surface monitoring. IPv4 only; needs root and masscan+nmap installed; can saturate links and DoS fragile networks, so start at a low `rate` in `scancannon.conf`.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Scan everything on a CIDR with ASN expansion

```
sudo ./scancannon.sh -c 203.0.113.0/24
```

Outcome: whois + RADB discovery of related networks with an interactive selection menu, then the masscan→nmap pass over the chosen ranges; results land under `./projects/<name>/results/` as `report.html`, `findings.csv` and categorized per-service lists (ssh_, http_, api_servers.txt...).

### Discover an org's whole network footprint from its domain

```
sudo ./scancannon.sh -d example.com
```

Outcome: resolves the domain's A records, walks whois to the owning ASN, pulls every announced prefix and lets you pick which to scan — external-recon scope building in one command.

### Scan a curated list of ranges without expansion

```
sudo ./scancannon.sh -f ranges.txt
```

Outcome: reads CIDRs one per line (`#` comments ignored) and scans them as-is, skipping ASN expansion and its interactive prompt; add `-F` to force expansion anyway.

### Add API detection and CVE hinting

```
sudo ./scancannon.sh -a -V -c 203.0.113.0/24
```

Outcome: besides service enumeration, HTTP services are NSE-fingerprinted and curl-probed for API endpoints (writes `api_servers.txt`, `api_details.txt`) and detected service versions are run through nmap's `vulners` NSE for CVE hints.

### Run unattended under a fixed project name

```
sudo ./scancannon.sh -p external-quarterly -f ranges.txt
```

Outcome: skips the interactive project menu, writes to `./projects/external-quarterly/`, and the generated report gains a *Changes Since Last Scan* section diffing new and disappeared host:port services against that project's previous run.

### Include UDP services in the sweep

```
sudo ./scancannon.sh -u -d example.com
```

Outcome: adds an nmap UDP pass over ports 53, 161 and 500 (DNS, SNMP, ISAKMP) — significantly slower, but completes the high-value surface.
