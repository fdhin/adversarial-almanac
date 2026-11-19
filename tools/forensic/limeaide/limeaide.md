---
name: "limeaide"
categories:
  - blackarch-forensic
version: "v2.0.0.r3.gce3c9b7"
homepage: "https://github.com/kd8bny/LiMEaide"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "kd8bny/LiMEaide"
github-stars: 157
github-pushed: "2020-08-26"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# limeaide

> Automated remote Linux memory acquisition: dump RAM over SSH and have the matching Volatility profile built for you.

## What it's for

LiMEaide (Daryl Bennett) wraps the LiME kernel module to acquire RAM from a Linux client and, on your workstation, build the matching Volatility profile. Point it at a remote host over SSH and it profiles the client's distribution, kernel and architecture, compiles or reuses the LiME module, loads it remotely and pulls the memory image back — over SFTP by default, or over a TCP socket so the image is never written to the client's disk; it can also run locally from removable media. It is an acquisition tool, not an analyzer: the deliverable is a memory image (raw, lime or padded format) plus a Volatility profile. Limits: it expects bash on the client, and building the module remotely requires matching kernel headers unless you pre-place a `.ko` in its profiles directory. The concept builds on Hal Pomeranz's Linux Memory Grabber.

## Typical use cases

### Dump a remote client's RAM over SSH

The one-argument acquisition.

```
limeaide 198.51.100.10
```

Outcome: connects over SSH, profiles the client, builds and loads the LiME module, transfers the memory image back via SFTP and builds a Volatility profile; output lands in a per-run output directory (prefix a case number with `-c`, name the output with `-o`, choose the image format with `-f raw|lime|padded`).

### Acquire as a non-root user with key auth

Run the grab under sudo when direct root is not granted.

```
limeaide -u user -k ~/.ssh/id_ed25519 198.51.100.10
```

Outcome: authenticates with the SSH key and executes the memory grab as the sudo user `user` instead of root; otherwise the same acquisition pipeline.

### Stream the image over a socket

Avoid writing the memory image to the client's disk.

```
limeaide -s 4444 198.51.100.10
```

Outcome: SSH is still used for control and helper files, but the memory image streams back over the TCP socket on port 4444 and is never written on the client (wire compression is unavailable in this mode).

### Skip the profiler

Supply distro, kernel and architecture when you already know them.

```
limeaide -p ubuntu 5.15.0-generic x86_64 198.51.100.10
```

Outcome: skips remote profiling and proceeds straight to acquisition with the given distribution, kernel version and architecture — faster when the client matches a module or profile you already have.

### Acquire the local host

Grab RAM from a system you are standing at.

```
limeaide local
```

Outcome: runs entirely on the local machine from its working directory — no network sockets are opened and no data is transferred elsewhere — producing image and profile as usual.

*No corpus evidence — derived from upstream documentation.*
