---
name: "dirbuster"
categories:
  - blackarch-scanner
version: "1.0_RC1"
homepage: "http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2018-11-14"
build-system: scripts
license: LGPL
meta-checked: "2026-11-09"
github-repo: "KajanM/DirBuster"
github-stars: 156
github-pushed: "2024-04-19"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dirbuster

> OWASP Java directory/file brute-forcer with a GUI, a headless mode, and the canonical directory-list wordlists.

## What it's for

DirBuster brute-forces directory and file names on web/application servers,
driven either from its Swing GUI or from a headless command-line mode. The
original OWASP project ended at 1.0-RC1 (its ideas live on in OWASP ZAP's
forced-browse add-on), but the package remains the canonical source of the
`directory-list-*` wordlists — `directory-list-2.3-medium.txt` and
`-small.txt`, the `lowercase` variants, `directory-list-1.0.txt`,
`apache-user-enum-*.txt` — which nearly every other content-discovery tool
consumes from `/usr/share/wordlists/dirbuster/` (or
`/usr/share/dirbuster/wordlists/`). Distro packages wrap the jar in a
`dirbuster` launcher that passes arguments straight to the JVM.

## Typical use cases

### Interactive GUI scan

Drive a directory brute-force from the graphical console.

```
dirbuster -u http://target.example.com/
```

Outcome: opens the "OWASP DirBuster - Web Application Brute Forcing"
window with the target URL prepopulated; pick a wordlist (e.g.
`directory-list-2.3-medium.txt`), set extensions and thread count, and
press Start — discovered paths accumulate in the results tree and the
response view until the run finishes ("DirBuster stopped"). Launching
`dirbuster` with no arguments opens the same GUI unpopulated.

### Headless scan from the command line

Run the same engine without a GUI, e.g. over SSH or in automation.

```
dirbuster -H -u http://target.example.com/ -l /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e php -t 20 -r dirbuster-report.txt
```

Outcome: `-H` starts headless mode (no window) and auto-saves the report on
exit; `-l` selects the wordlist, `-e` the file extensions to append, `-t`
the thread count, and `-r` the report path — without `-r` the report lands
in `DirBuster-Report-<host>-<port>.txt` in the working directory.

### Use the DirBuster wordlists elsewhere

The shipped lists work as dictionaries for any fuzzer.

```
gobuster dir -u http://target.example.com/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php
```

Outcome: enumerates the target with the DirBuster medium list;
`directory-list-2.3-small.txt` is the faster first pass and the
`directory-list-lowercase-2.3-small.txt` variant suits case-sensitive
servers.

*Corpus evidence: the corpus shows both usage shapes — actual GUI runs of the
1.0-RC1 console, and, far more often, references to its packaged wordlists
(`directory-list-2.3-medium.txt`, `-small.txt`, lowercase and 1.0 variants)
passed to gobuster/ffuf/wfuzz. Headless flags verified against the packaged
jar's own `-h` output.*
