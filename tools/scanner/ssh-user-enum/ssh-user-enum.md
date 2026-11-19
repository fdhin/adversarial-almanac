---
name: "ssh-user-enum"
categories:
  - blackarch-scanner
version: "7.ae453c1"
homepage: "https://github.com/nccgroup/ssh_user_enum"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/ssh_user_enum"
github-stars: 95
github-pushed: "2016-08-30"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ssh-user-enum

> Enumerates SSH usernames from authentication timing — valid usernames take measurably longer to fail than nonexistent ones.

## What it's for

ssh_user_enum (NCC Group, Python 2 + paramiko, packaged as `ssh-user-enum` with the script `ssh_enum.py`) is a username enumerator built on an SSH timing side channel: it authenticates with an oversized password, because a valid username makes the server spend real time processing it (the tool's multiplier controls the induced delay), and classifies each candidate by whether the response time crosses a threshold. It distinguishes existing users from nonexistent ones without ever succeeding at a login, which feeds username lists for password attacks. It only works against SSH servers that still exhibit the timing difference (older OpenSSH-era behavior; patched servers give uniform timings), and it opens one connection per guess.

## Typical use cases

### Enumerate users with autotuning

```
ssh-user-enum -u users.txt -i 198.51.100.10 -a
```

Outcome: autotune mode first calibrates the optimum multiplier and threshold against the target, then tests every username in `users.txt` and reports which ones exist; the chosen multiplier/threshold values are printed so they can be reused on subsequent runs.

### Reuse tuned values against a non-standard port

```
ssh-user-enum -u users.txt -i 198.51.100.10 -p 2222 -m 4000 -t 8
```

Outcome: skips calibration and runs the same timing attack with the previously measured multiplier (4000) and threshold (8) against SSH on port 2222 — faster and more consistent for repeated runs against the same host.

*No corpus evidence — derived from upstream documentation.*
