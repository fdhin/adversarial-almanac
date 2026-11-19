---
name: "nerva"
categories:
  - blackarch-fingerprint
version: "v1.83.0.r0.g360d891"
homepage: "https://github.com/praetorian-inc/nerva"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "praetorian-inc/nerva"
github-stars: 378
github-pushed: "2026-11-06"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 499
gh-activity-checked: "2026-11-09"
---

# nerva

> Fast single-command service fingerprinting across 170+ TCP, UDP and SCTP protocols, with version metadata extraction and misconfiguration detection.

## What it's for

Nerva identifies which network service is running on an already-open port. You point it at `host:port` targets — typically produced by a port scanner such as naabu or nmap — and it probes them with protocol plugins covering databases, remote access, web services, messaging, industrial and telecom protocols, reporting the identified protocol plus any version and configuration metadata it extracts (76 of the plugins fingerprint HTTP-based technologies). It complements rather than replaces a port scanner: it assumes the ports you hand it are open and does no host discovery. Optional checks flag common security misconfigurations such as an unauthenticated Docker API, cleartext telnet, or weak SSH ciphers. Output is human-readable by default, JSON or CSV on request, and a Go library API exists for embedding.

## Typical use cases

### Fingerprint a single open port

```
nerva -t example.com:22
```

Outcome: probes the target and prints one line per port in `protocol://host:port` form, e.g. `ssh://example.com:22`.

### Get full metadata as JSON

```
nerva -t example.com:22 --json
```

Outcome: prints a JSON object per service with host, ip, port, protocol, transport and a metadata block (versions, configurations); suitable for piping into `jq`.

### Fingerprint a list of targets from a file

```
nerva -l targets.txt --json -o results.json
```

Outcome: fingerprints every `host:port` line in `targets.txt` and writes the collected JSON results to `results.json`.

### Chain fingerprinting behind a port scanner

```
naabu -host example.com -silent | nerva
```

Outcome: reads `host:port` lines from stdin and prints an identified service URL for each open port, so discovery and fingerprinting run as one pipeline (nmap output works the same way).

### Fingerprint a UDP service

```
nerva -t example.com:53 --udp
```

Outcome: probes UDP port 53 and reports the service found (e.g. `dns://example.com:53`); SCTP works the same way with `--sctp` (Linux only).

### Flag security misconfigurations

```
nerva -t example.com:2375 --misconfigs --json
```

Outcome: JSON output additionally carries a `security_findings` array with finding id, severity and evidence per finding — e.g. `docker-unauth-api` (critical) when the Docker API answers without authentication.

### Sweep a large list with bounded parallelism

```
nerva -l large-target-list.txt --fast -W 100 -H 5 -R 50 -v
```

Outcome: fingerprints only each host's default ports (`--fast`) using at most 100 concurrent workers (`-W`), 5 simultaneous connections per host (`-H`) and 50 scans/second (`-R`), printing progress verbosely; Ctrl+C still returns partial results.

### Route scans through a proxy

```
nerva -t target.internal:80 --proxy socks5://127.0.0.1:1080 --dns-order p
```

Outcome: tunnels probing traffic through the SOCKS5 proxy and resolves target names proxy-side (`--dns-order p`); `socks5h://` forces this implicitly, and `--proxy-auth user:pass` supplies credentials.

*No corpus evidence — derived from upstream documentation.*
