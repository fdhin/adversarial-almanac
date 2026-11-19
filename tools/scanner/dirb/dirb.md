---
name: "dirb"
categories:
  - blackarch-scanner
version: "2.22"
homepage: "http://dirb.sourceforge.net/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2015-10-22"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
worth: 1.5
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dirb

> Classic dictionary-based web content scanner that also ships the de-facto standard directory wordlists.

## What it's for

dirb launches dictionary attacks against a web server and analyzes the
responses to find existing or hidden web objects (directories, files, CGI
endpoints). It is a content scanner, not a vulnerability scanner: it does not
spider HTML links and does not look for bugs — it tests for existence, and
can amplify every wordlist entry with file extensions. Beyond the binary,
the package installs a widely reused wordlist collection under
`/usr/share/dirb/wordlists/` — `common.txt` for quick sweeps, `big.txt` for
thorough ones, plus `small.txt`, language lists, `extensions_common.txt`,
`mutations_common.txt`, and the `others`/`stress`/`vulns` subdirectories —
which modern fuzzers (gobuster, ffuf, wfuzz, feroxbuster) consume directly;
for many installations that wordlist pack is the reason dirb is still
installed.

## Typical use cases

### Quick content sweep with the common wordlist

Find which directories and files exist on a web root.

```
dirb http://target.example.com/ /usr/share/dirb/wordlists/common.txt
```

Outcome: recursively fuzzes the target with the common list, printing each
discovered URL with its response code and size (`+ http://target.example.com/admin (CODE:301|SIZE: 0)` style lines), recursing into found
directories, and ending with `DOWNLOADED`/`FOUND` totals; several wordlist
files may be listed after the URL to chain them.

### Thorough sweep with extension amplification

Hunt files that only exist with an extension applied.

```
dirb http://target.example.com/ /usr/share/dirb/wordlists/big.txt -X .php,.html,.bak
```

Outcome: tests every wordlist entry and, via `-X`, every entry again with
each extension appended, multiplying coverage; `big.txt` makes the run much
longer, so it is the second-pass choice after `common.txt`. `-x <file>`
does the same with an extensions file such as the packaged
`extensions_common.txt`.

### Save a clean report

Capture findings without the per-word progress noise.

```
dirb http://target.example.com/ -o dirb-report.txt -S
```

Outcome: runs the default wordlist against the target, writes the full
result to `dirb-report.txt` (`-o`), while `-S` suppresses the on-screen
listing of every word tested so the terminal only shows hits.

### Feed dirb's wordlists to a faster fuzzer

Use the packaged lists from other content-discovery tools.

```
gobuster dir -u http://target.example.com/ -w /usr/share/dirb/wordlists/common.txt -x php
```

Outcome: gobuster (or ffuf/wfuzz with the same `-w` path) enumerates the
target using dirb's dictionary — the standard way these lists are consumed
today; swap in `big.txt` or the `stress`/`vulns` lists for deeper passes.

*Corpus evidence: the corpus references dirb exclusively through its installed
wordlists (paths like `/usr/share/dirb/wordlists/common.txt` and
`/usr/share/wordlists/dirb/common.txt` passed to gobuster, ffuf, wfuzz and
feroxbuster); no direct `dirb` invocations appear. CLI examples derived
from the upstream dirb 2.22 README and man page.*
