---
name: "rdpassspray"
categories:
  - blackarch-cracker
version: "33.c1ba58e"
homepage: "https://github.com/xFreed0m/RDPassSpray"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "xFreed0m/RDPassSpray"
github-stars: 676
github-pushed: "2023-08-17"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rdpassspray

> RDP password spraying (and pass-the-hash) against Windows targets, driven through xfreerdp.

## What it's for

Password spraying over RDP in a Microsoft domain environment: it tests one (or a few) password(s) across many user accounts by wrapping xfreerdp logons. Lowercase flags (`-u`, `-p`, `-t`) take a single username, password or target, uppercase flags (`-U`, `-P`, `-T`) take files with one entry per line, so the same tool covers single checks and large sprays. It supports fixed (`-s`) or randomized (`-r`) throttling between attempts to stay under account-lockout thresholds, pass-the-hash with NTLM hashes via `--pth`, spoofed client hostnames (`-n`) so failed-logon events (event ID 4625) do not record the attacking machine's IP, and CSV output (`-o`). It authenticates over RDP only — not SMB or WinRM — and the lockout policy must be checked before spraying or accounts will be locked.

## Typical use cases

### Quick single spray

Test one password for one user against one host.

```
rdpassspray -u user -p Password1 -d CORP -t 198.51.100.10
```

Outcome: attempts a single RDP logon for that user/host pair and reports success or failure on the console.

### Spray a domain from lists with throttling

One password list across many users and hosts, paced to avoid lockouts.

```
rdpassspray -U users.txt -P passwords.txt -T targets.txt -s 30 -o results.csv
```

Outcome: iterates the users, passwords and targets files with a 30-second pause between attempts, and appends each attempt's result to `results.csv`; valid pairs can be filtered out of the CSV afterwards.

### Pass-the-hash over RDP

Reuse captured NTLM hashes instead of plaintext passwords.

```
rdpassspray -u administrator -P ntlm_hashes.txt --pth -t 198.51.100.10
```

Outcome: treats each line of `ntlm_hashes.txt` as an NTLM hash and attempts pass-the-hash RDP authentication for the account, reporting which hashes produce a successful logon.

*No corpus evidence — derived from upstream documentation.*
