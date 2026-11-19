---
name: "eazy"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://packetstormsecurity.com/files/117572/EAZY-Web-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# eazy

> Interactive Python 2 scanner that reverse-IP-expands a host into a website list, then probes every site for admin panels, web shells, backups and install folders with HEAD requests.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Bulk, shallow discovery of common web exposures across many sites at once. EAZY ("EAZY Finder", by r0ckin) works from a domain list: it can build one for you by reverse-IP lookup of a single IP through third-party services (viewdns.info, sameip.org) into `dom.txt`, then walks every listed site issuing HEAD requests for paths drawn from its bundled wordlists — `admin.txt` (admin panels), `shell.txt` (uploaded web shells), `zip.txt` (archive backups), `dir.txt` (sensitive directories), `upload.txt` and `install.txt` — recording hits into an HTML report, `rezult.html`. It is fully interactive (no command-line flags; every choice is a yes/no prompt) and detection is status-code based, so a 200/301 on a guessed path is a lead to verify, not a confirmed finding. A 2013 Python 2-era tool distributed as a PacketStorm archive; it has no canonical repository — the same-named GitHub project is an unrelated framework.

## Typical use cases

### Run the interactive scan

```
eazy
```

Outcome: the packaged launcher starts the script inside its wordlist directory; answer `yes` to the reverse-IP prompt and supply an IP to populate `dom.txt` automatically (or answer `no` and edit `dom.txt` yourself), then pick which probe categories to enable — discovered paths are appended to `rezult.html` as a clickable report.
