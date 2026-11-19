---
name: "dbd"
categories:
  - blackarch-misc
version: "61.8cf5350"
homepage: "https://github.com/gitdurandal/dbd"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2019-08-12"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "gitdurandal/dbd"
github-stars: 151
github-pushed: "2023-06-18"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dbd

> A portable Netcat clone for Unix and Win32 that encrypts every byte with AES-CBC-128 + HMAC-SHA1 under a shared secret.

## What it's for

dbd ("Durandal's Backdoor", by Kyle Barnthouse) is a TCP-only Netcat-style tool: it connects, listens, pipes stdin/stdout, and can execute a program after connecting. Unlike plain netcat, the channel is encrypted with AES-CBC-128 and authenticated with HMAC-SHA1 (implementation by Christophe Devine), keyed by a secret phrase shared between both ends (`-k`). Reach for it when you need an interactive shell or data pipe between two hosts and want the traffic opaque rather than cleartext. It is not a scanner, tunnel, or proxy; and because a default key phrase is compiled in, always pass an explicit `-k`.

## Typical use cases

### Serve an encrypted remote shell

Start the listener on the host that should receive connections; both ends must use the same secret.

```
dbd -l -p 4444 -e /bin/bash -k secret -v
```

Outcome: dbd listens on TCP 4444; a peer running dbd with the same `-k` secret gets an encrypted interactive bash session (`-e cmd.exe` on Windows).

### Connect back to the listener

Run on the client side to reach the listener above.

```
dbd -k secret 198.51.100.10 4444
```

Outcome: opens an encrypted channel to 198.51.100.10:4444; keystrokes are executed by the remote `-e` program and its output returns encrypted.

### Keep the listener alive across disconnects

```
dbd -l -p 4444 -e /bin/bash -k secret -r 0
```

Outcome: `-r 0` re-listens immediately after each disconnect, daemon-style; a nonzero `-r n` instead reconnects with an n-second pause between attempts.

### Drop encryption for netcat interop

```
dbd -c off -l -p 4444 -e /bin/bash
```

Outcome: `-c off` disables the crypto layer, so dbd behaves like a standard netcat listener with program execution and can talk to a plain `nc` peer — at the cost of plaintext traffic.

*No corpus evidence — derived from upstream documentation.*
