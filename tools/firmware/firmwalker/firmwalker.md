---
name: "firmwalker"
categories:
  - blackarch-firmware
version: "101.23ff299"
homepage: "https://github.com/craigz28/firmwalker"
packaged-by: [blackarch]
blackarch-builddate: "2020-10-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "craigz28/firmwalker"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# firmwalker

> Bash script that sweeps an extracted or mounted firmware filesystem for credentials, certificates, configuration files, and other quick wins.

*No corpus evidence — derived from upstream documentation (the upstream repository has since been removed from GitHub; the archived README is the source).*

## What it's for

First-pass triage of an unpacked firmware image. Instead of manually grepping a rootfs dumped by binwalk or firmware-mod-kit, firmwalker runs a fixed battery of searches: `etc/shadow` and `etc/passwd` contents, the `etc/ssl` tree, certificate and key files (`.pem`, `.crt`, and similar), configuration and script files, other `.bin` blobs, keywords such as `admin`, `password` and `remote`, web servers common on IoT devices, service binaries like `ssh`, `tftp` and `dropbear`, and URLs, e-mail addresses and IP addresses. It does not extract firmware itself — feed it a directory that is already extracted or mounted, and read the results from its report file. If eslint is installed, the script also runs its JavaScript static-analysis portion on any scripts it finds. On BlackArch the script is wrapped as `firmwalker`; from a checkout run `./firmwalker.sh`.

## Typical use cases

### Triage an extracted firmware root filesystem

```
./firmwalker.sh fmk/rootfs ../firmwalker.txt
```

Outcome: writes `firmwalker.txt` (or the path given as the second argument) containing every hit from the search battery — credential files, SSL material, configs, scripts, binaries, keywords, and embedded URLs, e-mail addresses and IP addresses. Do not place the script inside the directory being searched, or it will scan itself and its own output file.
