---
name: "cero"
categories:
  - blackarch-scanner
version: "v1.3.0.r19.gb73125b"
homepage: "https://github.com/glebarez/cero"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2023-07-10"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "glebarez/cero"
github-stars: 700
github-pushed: "2024-03-31"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cero

> Scrapes domain names out of the TLS certificates presented by arbitrary hosts, on any port that speaks TLS.

## What it's for

Cero connects to remote hosts, completes a TLS handshake, and reads the domain names (SAN/CN entries) out of the certificate the host presents. It is not limited to HTTPS — any TLS-based protocol (e.g. SMTPS) works if you point it at the right port. Targets can be domain names, bare IPs, CIDR ranges, or IPv6, and it runs concurrently, so it fits naturally into a recon pipeline to expand a scope from live infrastructure. It only extracts names from certificates; it does not port-scan and says nothing about the service behind the cert.

## Typical use cases

### Scrape names from a single host

```
cero example.com
```

Outcome: connects to example.com on the default port 443 and prints every domain name found in its certificate, one per line (e.g. `example.com`, `*.example.com`).

### Mass-scrape a CIDR on several TLS ports

```
cero -p 443,8443,10443 -c 1000 198.51.100.0/24
```

Outcome: sweeps the whole /24 on the listed ports with 1000 concurrent workers, printing scraped names as they arrive; per-target port overrides (`198.51.100.10:8443`) and IPv6 forms (`[2001:db8::1]:10443`, `2001:db8::/32`) are accepted the same way.

### Clean piped input for pipelines

```
cat targets.txt | cero -c 500 -d
```

Outcome: reads targets from stdin and strips invalid entries (wildcards, bare IPs, usual gibberish) from the output so downstream tools receive a clean domain list.

### Verbose per-host detail

```
cero -v example.com example.com:80
```

Outcome: prints one line per host — the scraped names in brackets on stdout, or the handshake error (e.g. "first record does not look like a TLS handshake") on stderr; `2>/dev/null` keeps only the successful hosts.

*No corpus evidence — derived from upstream documentation.*
