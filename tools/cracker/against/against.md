---
name: "against"
categories:
  - blackarch-cracker
version: "0.2"
homepage: "http://nullsecurity.net/tools/cracker.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# against

> Combined SSH port scanner and parallel brute-forcer in one self-contained script.

## What it's for

against (nullsecurity.net) chains two jobs: a multithreaded TCP-connect scan that discovers hosts with an open SSH port, and a multithreaded password attack that runs against every discovered host concurrently. Targets come from a single IP or domain (`-i`), an address range such as `198.51.100.1-254` (`-i`), a file (`-I`), or randomly generated addresses (`-r`). It is written for Python 2 (the packaged copy is patched to `env python2` and needs python2-paramiko), so run it with a Python 2 interpreter. Defaults are port 22, user root, password toor — blunt online guessing that key-only SSH or a brute-force blocker defeats outright.

## Typical use cases

### Attack a single host with a password list

```
against -i target.example.com -u root -L passwords.txt
```

Outcome: skips discovery for a single named target, brute-forces SSH as root with every password in the list, and prints any working credential.

### Discover and attack an address range

```
against -i 198.51.100.1-254 -U users.txt -L passwords.txt -o creds.txt -O live-hosts.txt
```

Outcome: TCP-connect-scans the range for open SSH ports, then attacks every live host in parallel; accepted logins are appended to creds.txt and reachable hosts to live-hosts.txt.

### Attack a prepared target list on a nonstandard port

```
against -I targets.txt -p 2222 -t 8
```

Outcome: reads one target per line from the file and attacks SSH on port 2222 with 8 threads per host, using the default root/toor credential pair.

*No corpus evidence — derived from upstream documentation.*
