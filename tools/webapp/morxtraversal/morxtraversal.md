---
name: "morxtraversal"
categories:
  - blackarch-webapp
version: "1.0"
homepage: "http://www.morxploit.com/tools/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# morxtraversal

> Checks a single HTTP GET parameter for path traversal.

## What it's for

MorXTraversal v1.0 (Simo Ben youssef, MorXploit Research, 2014) is a focused Perl script that tests whether a given parameter in a URL can be walked out of its directory. You supply the host, the script-and-parameter prefix (e.g. `index.php?file=`), the target file to read (classically `/etc/passwd`), a signature string expected in that file's content (e.g. `root`), and the maximum directory depth to climb; the script then prepends increasing numbers of `../` segments until the response body contains the signature. It handles exactly one parameter at a time and only via GET; there is no encoding-variant fuzzing, so it proves the simple case only.

## Typical use cases

### Test a parameter for basic traversal

```
morxtraversal target.example.com 'index.php?file=' '/etc/passwd' root 10
```

Outcome: walks `../` prefixes from 1 to 10 against the parameter and prints `Path N failed, next ..` per attempt; a hit prints `[+] http://target.example.com/index.php?file=../../../../etc/passwd success!` — the depth shown is your working traversal prefix.

### Test with additional fixed query parameters

```
morxtraversal target.example.com 'download.php?page=' '/etc/passwd' root 15 '&lang=en&id=1'
```

Outcome: same climb, with the extra parameter string appended to every request so the target page renders normally — necessary when the vulnerable code path only executes with other arguments present.

No corpus evidence — derived from the packaged source (`morxtraversal.pl`, usage block and banner).
