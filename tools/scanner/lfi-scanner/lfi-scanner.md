---
name: "lfi-scanner"
categories:
  - blackarch-scanner
version: "4.0"
homepage: "http://packetstormsecurity.com/files/102848/LFI-Scanner.0.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BlackArch/blackarch"
github-stars: 3563
github-pushed: "2026-11-08"
github-archived: false
github-issues: 35
github-match: "search"
gh-checked: "2026-11-09"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1007
gh-activity-checked: "2026-11-09"
---

# lfi-scanner

> Interactive Perl scanner that probes a single URL for local file inclusion.

## What it's for

Viper LFI Scanner 4.0 (by Bl4ck.Viper, packaged as `lfi-scanner`) tests one target URL for local file inclusion. It offers two menu modes: an `/etc/` scan that tries to include passwd and log files across traversal depths (with a bypass variant), and an environ check that tests `/proc/self/environ` for User-Agent injection. Pure Perl on LWP, menu-driven, no command-line flags. Per its own notes it only finds simple LFI — no filter-evasion or Windows targets. For a newer, automatic LFI exploiter see the companion note [[lfisuite]] — the two are unrelated projects by different authors. The original author's release has no GitHub home; the packaged Perl script is carried verbatim (sha512-identical to the PKGBUILD digest) as `packages/lfi-scanner/lfi-scanner-ver4.0.pl.txt` inside the BlackArch package collection, which is what this note's GitHub link and stats describe — the distro collection, not the author.

## Typical use cases

### Probe a URL for LFI

Interactively test a single suspect parameter.

```
lfi-scanner
```

Outcome: prints a two-entry menu (1 = passwd/log scan of `/etc/`, 2 = environ injection via User-Agent); pick a mode and paste the target including the vulnerable parameter, e.g. `http://target.example.com/index.php?page=` — the script walks directory depths and reports which inclusions succeed.
