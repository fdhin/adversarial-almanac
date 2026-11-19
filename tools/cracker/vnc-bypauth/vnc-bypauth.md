---
name: "vnc-bypauth"
categories:
  - blackarch-cracker
version: "0.0.1"
homepage: "http://pentester.fr/resources/tools/techno/VNC/VNC_bypauth/"
packaged-by: [blackarch]
blackarch-builddate: "2022-08-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# vnc-bypauth

> Multi-threaded scanner that finds RealVNC 4.1.0–4.1.1 servers vulnerable to the null-authentication bypass.

## What it's for

VNC_bypauth (class101, 2006) detects RealVNC servers up to and including 4.1.1 that fail to verify the client's challenge response, a flaw that grants access with any password. The tool performs the VNC handshake against each host and port and classifies every endpoint — vulnerable, patched, banned, licence-expired or not VNC at all — rather than exploiting it: bringing up an actual session afterwards is left to a normal VNC client. It scans IP and port ranges with up to 500 threads by default, reports a live progress table, and writes confirmed findings to `VNC_bypauth.txt` in the working directory. As 32-bit-era compiled software it targets the 2006 vulnerability (CVE-2006-2369), long fixed in any maintained VNC server.

## Typical use cases

### Check a host for the VNC auth-bypass flaw

Point the VNC scanner at a single target on the standard VNC port.

```
vnc-bypauth -i 198.51.100.10 -p 5900 -vnc -vv
```

Outcome: connects, performs the RFB handshake and prints one status line per attempt — `vnc4:VULNERABLE`, `vnc4:patched` or `not_vnc4:*` — alongside a live FOUND/PORT/IP/STATUS/THREADS progress table; any vulnerable host is also appended to `VNC_bypauth.txt`.

### Locate VNC on non-default ports first

Use the TCP connect scan across a port range before the VNC-specific check.

```
vnc-bypauth -i 198.51.100.10 -p 5900-5906 -cT
```

Outcome: reports which ports in the range accept a plain TCP connection, so displays moved off 5900 can be found before running the `-vnc` authentication check against them.

### Print the built-in flag help

Run the tool with a single argument-group flag to get its details.

```
vnc-bypauth -p
```

Outcome: prints the MAIN MENU help listing the target flags (`-i` IP/range, `-p` port/range), scan types (`-vnc`, `-cT`) and the verbosity/timeout/thread options; raising the stack limit first (`ulimit -s unlimited`) lets the scanner run its full default 500 threads safely.

*No corpus evidence — derived from upstream documentation.*
