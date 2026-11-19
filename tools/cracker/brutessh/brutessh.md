---
name: "brutessh"
categories:
  - blackarch-cracker
version: "0.6"
homepage: "http://www.edge-security.com/edge-soft.php"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# brutessh

> Multithreaded SSH wordlist attack against one host and one user.

## What it's for

brutessh is Christian Martorella's (Edge-Security) Python script for dictionary attacks against an sshd service: it takes a target host, a single username and a password list, and runs the attempts through a pool of paramiko SSH connections, 12 threads by default. The option set is deliberately minimal — host, username, password file, thread count and port — and each run covers exactly one user on one host; there is no user-list mode, no key authentication and no scheduling or throttling logic. It is a Python 2-era tool: BlackArch still packages it running under `python2` with a bundled old paramiko, and it writes its paramiko trace to `demo.log` in the working directory. Its original distribution site is offline; the source survives in package archives.

## Typical use cases

### Run a wordlist against sshd

Try a password file for one user on one host.

```
brutessh -h 198.51.100.10 -u user -d passwords.txt
```

Outcome: attempts tick by with a live `n/total` counter and the current candidate; on success the tool prints `Auth OK ---> Password Found: <password>` and exits. From the unpacked source tree the same invocation is `python2 brutessh.py -h 198.51.100.10 -u user -d passwords.txt`.

### Target a non-standard port with a larger thread pool

Adjust concurrency and port for the target's sshd.

```
brutessh -h 198.51.100.10 -p 2222 -u user -d passwords.txt -t 24
```

Outcome: the same attack runs against port 2222 with 24 concurrent connections; the author notes that raising the default 12 threads can hurt reliability, so tune downward if attempts start failing.

*No corpus evidence — derived from upstream documentation.*
