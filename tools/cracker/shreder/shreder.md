---
name: "shreder"
categories:
  - blackarch-cracker
version: "115.a108575"
homepage: "https://github.com/EntySec/Shreder"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "EntySec/Shreder"
github-stars: 220
github-pushed: "2024-07-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shreder

> Multi-threaded SSH password brute-forcer with a simple CLI and a Python API.

## What it's for

EntySec's Shreder brute-forces the password of a single SSH target from a
wordlist, built on paramiko. With no delay set it averages roughly one
attempt per 0.1 seconds (about 1000 passwords in under two minutes), and an
optional delay spaces attempts out for lockout-prone services. Scope is
deliberately narrow: one host, one username and one password list per run —
no username spraying or multi-host scanning. The same functionality is
importable as a Python module for use inside larger tooling.

## Typical use cases

### Brute-force one SSH host with a wordlist

Point it at a target together with a username and a password list.

```
shreder 198.51.100.10 -u user -l passwords.txt
```

Outcome: attempts each password over SSH; on success the recovered
password is printed and the run stops.

### Throttle attempts against a nonstandard port

Combine `-p` for the SSH port with `-d` for a delay between login attempts.

```
shreder 198.51.100.10 -p 2222 -u user -l passwords.txt -d 2
```

Outcome: same brute-force run paced to one attempt every two seconds,
reducing the risk of tripping protection on the target.

### Drive the brute-force from Python code

Import Shreder and call its brute method directly.

```python
from shreder import Shreder

shreder = Shreder()
print(shreder.brute('198.51.100.10', 22, 'user', 'passwords.txt'))
```

Outcome: performs the same single-target attack inside a Python program and
returns the recovered password when one is found.

*No corpus evidence — derived from upstream documentation.*
