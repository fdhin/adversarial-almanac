---
name: "cjdnsify-git"
categories:
  - defensive
version: "20140717.r6.bc354af"
homepage: "https://github.com/prurigro/cjdnsify"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "prurigro/cjdnsify"
github-stars: 7
github-pushed: "2023-12-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cjdnsify-git

> LD_PRELOAD-based wrapper that confines a program's network access to the cjdns encrypted IPv6 overlay.

## What it's for

cjdnsify runs a command wrapped in force_bind's LD_PRELOAD shim so that the sockets the program opens are bound to the cjdns IPv6 address of your tun device — the program can then only talk to peers on the cjdns mesh, not the clearnet. It reads /etc/default/cjdnsify for the force_bind.so location and the tun device name, and requires a running cjdroute. It works for LD_PRELOAD-compatible, dynamically linked programs (the author verified curl, wget, lynx, links and browsers among others); setuid binaries and raw-socket tools like ping are not confined. Use it to fetch from or serve to the overlay without risking a clearnet leak, and test each program to confirm the restriction took effect.

## Typical use cases

### Fetch a file over cjdns only

Download from an overlay peer with clearnet access cut off.

```
cjdnsify wget http://[fc00::1]:8080/file.txt
```

Outcome: wget runs with its sockets forced onto the cjdns tun device's IPv6 address; the download succeeds only via the overlay, and attempts to reach IPv4 or clearnet destinations fail — no traffic leaks outside cjdns.

### Browse an overlay service interactively

Open a cjdns-only host in a text browser.

```
cjdnsify lynx [fc00::1]:82
```

Outcome: lynx reaches the service exclusively over the encrypted overlay; trying a clearnet URL in the same session should fail, confirming the confinement is active for that program.

*No corpus evidence — derived from upstream documentation.*
