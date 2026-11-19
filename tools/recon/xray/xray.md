---
name: "xray"
categories:
  - blackarch-recon
version: "109.7e8cc67"
homepage: "https://github.com/evilsocket/xray"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-10-01"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "evilsocket/xray"
github-stars: 2337
github-pushed: "2024-07-10"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xray

> OSINT recon engine by evilsocket: brute-forces subdomains, enriches every host through Shodan, and presents the map in a local web UI.

## What it's for

XRay (Simone Margaritelli, evilsocket — not to be confused with other same-named scanners) automates the first hours of network reconnaissance: it enumerates subdomains from a wordlist over DNS, then asks Shodan for open ports and banners on each discovered address, optionally pulls per-domain history from ViewDNS, and runs its own grabbers (HTTP `Server`/`X-Powered-By` headers, `robots.txt` entries, TLS certificate chains with recursive CN/SAN expansion, HTML titles, DNS `version.bind`, MySQL/SMTP/FTP/SSH/POP/IRC banners). Findings land in a session file and a web UI. There is no port scanning from your machine — port intel comes via the Shodan API, so a `-shodan-key` is effectively mandatory (without it you get DNS enumeration only). The repository is legacy: the author reimplemented the concept in Rust as `legba`, which new work should prefer.

## Typical use cases

### Full OSINT run on a domain

Enumerate and fingerprint a target's external presence.

```
xray -shodan-key YOUR_SHODAN_API_KEY -domain example.com
```

Outcome: brute-forces subdomains (default wordlist, 16 concurrent consumers), enriches each unique IP with Shodan port/hostname data, grabs banners, saves state to `example.com-xray-session.json` and serves the interactive map at `http://127.0.0.1:8080/`.

### Add domain history and tune the enumeration

Include passive DNS history and control wordlist and concurrency.

```
xray -shodan-key YOUR_SHODAN_API_KEY -viewdns-key YOUR_VIEWDNS_API_KEY -domain example.com -wordlist wordlists/default.lst -consumers 32
```

Outcome: as above, plus historical records for each subdomain via the ViewDNS API, using your own wordlist and 32 parallel enumeration workers; `-preserve-domain` keeps the base domain in the enumeration set, `-port 9090` moves the web UI.

*No corpus evidence — derived from upstream documentation.*
