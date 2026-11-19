---
name: "orakelcrackert"
categories:
  - blackarch-windows
version: "1.00"
homepage: "https://github.com/vanhauser-thc/THC-Archive/blob/master/Tools/thc-orakelcrackert11g.tar.gz"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: windows
license: custom
meta-checked: "2026-11-09"
github-repo: "vanhauser-thc/THC-Archive"
github-stars: 776
github-pushed: "2025-09-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# orakelcrackert

> THC tool by vonJeek that cracks Oracle 11g's SHA1-based (SPARE4) password hashes against a wordlist.

## What it's for

OrakelCrackert attacks Oracle's 11g password protection algorithm: the SHA1-based hash stored in the SPARE4 column of DBA_USERS. You supply the 60-nibble SPARE4 hash (and optionally the old 3DES hash, 16 nibbles) plus a wordlist, and the tool performs password guessing — including the classic password-equals-username attempt — then recovers the mixed-case password, since the 11g hash is first computed case-insensitively. It is a Windows GUI executable ("OracleCrackert, an Oracle tool by vonJeek") distributed through the THC-Archive together with a wordlist.txt; there is no command-line interface, so on Linux it runs under Wine. It cracks; it does not extract hashes from a database — pull the target user's SPARE4 value out of the DB first.

## Typical use cases

### Launch the cracker in a lab

Start the GUI to work on a captured Oracle 11g hash.

```
wine /usr/share/orakelcrackert/thc-orakelcrackert11g.exe
```

Outcome: opens the OrakelCrackert window; paste the SPARE4 hash into the hash field, point it at the bundled `wordlist.txt` (or your own), and start guessing — output is written in the GUI ("BINGO the password is ..." on success).

### Inspect the packaged artifacts

Confirm what the package ships before moving it to an analysis machine.

```
ls /usr/share/orakelcrackert/ && file /usr/share/orakelcrackert/thc-orakelcrackert11g.exe
```

Outcome: lists the Windows PE32 GUI executable and the accompanying wordlist.txt; the executable is identified as a 32-bit Windows binary, which is what Wine executes.

*No corpus evidence — derived from the upstream archive itself (thc-orakelcrackert11g.tar.gz downloaded and inspected: executable strings name the author, hash lengths and guessing modes).*
