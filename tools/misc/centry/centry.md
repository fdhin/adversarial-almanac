---
name: "centry"
categories:
  - blackarch-misc
version: "72.6de2868"
homepage: "https://github.com/0xPoly/Centry"
packaged-by: [blackarch]
blackarch-builddate: "2018-08-06"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0xPoly/Centry"
github-stars: 146
github-pushed: "2022-02-07"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# centry

> A panic button that wipes RAM and cuts power to defeat cold-boot and DMA attacks.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Centry is an experimental (2018-era, Python 2 lineage) anti-forensics "panic button" for laptops using full-disk encryption: when triggered it locks the screen, unmounts TrueCrypt volumes and clears their key caches, zeroes RAM with sdmem, and forces an immediate ACPI shutdown so encryption keys do not linger in memory. It is most effective on FDE systems without swap. Triggering works from its small Tk GUI or over the network: each instance listens on UDP 29899 for a digest of the configured password salted with the current UTC minute (replay protection), and one node's panic broadcast propagates to every node sharing that password. It is protection at shutdown time only — not encryption, and not a defense while the machine sits unlocked.

## Typical use cases

### Arm the panic button

```
sudo centry 'Password1'
```

Outcome: opens the small Centry window and starts a background UDP listener on port 29899; pressing the GUI panic button immediately runs the panic routine — screen lock, TrueCrypt unmount, RAM wipe, forced power-off. Root is required for the memory-wipe and shutdown steps.

### Broadcast the panic signal to every node

```
python3 -c 'import socket,hashlib,datetime; p=b"Password1"; t=datetime.datetime.utcnow().isoformat()[:-10].encode(); h=hashlib.sha256(p+t).hexdigest().encode(); s=socket.socket(socket.AF_INET,socket.SOCK_DGRAM); s.setsockopt(socket.SOL_SOCKET,socket.SO_BROADCAST,1); s.sendto(h,("<broadcast>",29899))'
```

Outcome: sends the current minute's password digest as a UDP broadcast to port 29899, replicating Centry's own propagation code — every listening node sharing that password verifies the digest and panics. On the original Python 2 runtime this works as designed; on Python 3 the upstream listener compares bytes against a string digest and drops the signal, so test on your deployment before relying on the network trigger.
