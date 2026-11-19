---
name: "mwebfp"
categories:
  - blackarch-fingerprint
version: "16.a800b98"
homepage: "https://github.com/falcon-lnhg/mwebfp"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "falcon-lnhg/mwebfp"
github-stars: 63
github-pushed: "2016-02-22"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mwebfp

> Massive Web Fingerprinter: sweep an IP range, find every web server, and record server info, vhosts and screenshots.

## What it's for

The LowNoiseHG (LNHG) Massive Web Fingerprinter, by F4Lc0N, automates
large-scope web recon: given IP ranges, host names or a target file, it
port-scans with nmap, identifies open HTTP/HTTPS services (any ports, not just
80/443), records the relevant web-server information for each, optionally
enumerates virtual hosts, and can take a rendered screenshot of every page via
CutyCapt (which requires a GUI on the scanning host). Results are exported as
a report (CSV is the working format) into a per-run output directory. It was
built for triaging big external/internal ranges — finding critical assets,
easily-exploitable services and forgotten servers — not for deep single-host
testing.

## Typical use cases

### Sweep a CIDR range with screenshots

```
mwebfp -i 198.51.100.0/24 -o outdir -w yes -v no
```

Outcome: port-scans the range, fingerprints every web service found, saves a
screenshot of each rendered page (`-w yes`) into `outdir`, and writes the CSV
report — a rapid visual inventory of a whole subnet.

### Fingerprint a named host

```
mwebfp -n target.example.com -o outdir
```

Outcome: resolves and scans the named server, recording web-server details for
each open HTTP/HTTPS port; vhost enumeration stays off unless `-v yes` is
given.

### Sweep targets from a file across custom ports

```
mwebfp -f targets.txt -p 80,8080 -s 443,8443 -o outdir -t CSV
```

Outcome: reads IP addresses and/or ranges from `targets.txt`, checks the
listed HTTP and HTTPS ports instead of the defaults, and exports the report in
CSV format to `outdir`.

*No corpus evidence — derived from upstream documentation.*
