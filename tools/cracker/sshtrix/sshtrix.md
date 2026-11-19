---
name: "sshtrix"
categories:
  - blackarch-cracker
version: "0.0.3"
homepage: "http://nullsecurity.net/tools/cracker.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-12-18"
build-system: make
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sshtrix

> Lean, very fast multithreaded SSH login cracker built directly on libssh.

## What it's for

sshtrix (Levent Kayan / noptrix, nullsecurity.net) does one job: throw login pairs at SSH servers as fast as libssh allows, supporting both SSHv1 and SSHv2. Credentials come as colon-separated `user:pass` lines, or separate user and password lists, or single values on the command line; targets are one host, an IP range, or a host list whose lines can carry per-host ports. Found logins can be printed immediately and logged to a file, and the run can stop after the first hit. Deliberately minimal — no banner grabbing, no post-exploitation features; if results look wrong, the manpage advises fewer threads or a longer connect timeout.

## Typical use cases

### Crack with a colon-separated login file

The shortest invocation: host plus a user:pass list.

```
sshtrix -h target.example.com -m logins.lst
```

Outcome: multithreaded attempts (4 threads by default) with each line of `logins.lst` (`user:pass`) tried in turn; found pairs are printed, `-e` exits after the first hit.

### Spray a user list with one password

Try every username against a single password.

```
sshtrix -h target.example.com -p 2222 -l users.lst -K Password1
```

Outcome: each username in `users.lst` is tried with the fixed password on port 2222 — the classic password-spray pattern for verifying policy-compliant credentials.

### Sweep a host list with full cross product

Many hosts, users, and passwords at once.

```
sshtrix -R hosts.lst -l users.lst -k passes.lst -t 32 -f found.txt -v
```

Outcome: every host in `hosts.lst` (lines may be `host` or `host:port`) is attacked with the complete user×password cross product across 32 threads; found pairs are appended to `found.txt` and `-v` prints each attempt as it happens.

*No corpus evidence — derived from upstream documentation.*
