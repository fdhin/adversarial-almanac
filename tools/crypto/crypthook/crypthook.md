---
name: "crypthook"
categories:
  - blackarch-crypto
version: "18.690dcae"
homepage: "https://github.com/chokepoint/CryptHook"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-18"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "chokepoint/CryptHook"
github-stars: 122
github-pushed: "2020-07-14"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crypthook

> LD_PRELOAD wrapper that transparently encrypts any application's TCP/UDP traffic with AES-256-GCM.

## What it's for

CryptHook secures existing applications with symmetric encryption without touching their code: it hooks the base network system calls (send/sendto, recv/recvfrom) via `LD_PRELOAD` and encrypts every packet with AES-256 in GCM mode, deriving the key from a passphrase with PBKDF2, reconstructing per-packet IVs on the receiving end, and authenticating each packet. Any program that communicates through those calls — netcat-style tools, custom clients and servers — can be wrapped on both ends; traffic between two instances sharing the same `CH_KEY` passphrase is unreadable on the wire. The default salts, iteration count and passphrase are compiled into `crypthook.c` and must be changed for any real deployment; the only dependency is libcrypto/OpenSSL.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Wrap a TCP client/server pair with encrypted transport

```
LD_PRELOAD=./crypthook.so CH_KEY=donthackmebro ncat -l -p 5000
LD_PRELOAD=./crypthook.so CH_KEY=donthackmebro ncat target.example.com 5000
```

Outcome: the listener (first command, on the server) and the client (second command, on the client host) both route their socket traffic through the hook; a packet capture on the path shows AES-256-GCM ciphertext instead of the plain ncat session while both endpoints see normal cleartext.

### Wrap UDP datagram traffic the same way

```
LD_PRELOAD=./crypthook.so CH_KEY=donthackmebro ncat -u -l -p 5000
LD_PRELOAD=./crypthook.so CH_KEY=donthackmebro ncat -u target.example.com 5000
```

Outcome: the datagram syscalls (sendto/recvfrom) are hooked the same way, giving an encrypted UDP tunnel between the two endpoints — useful for wrapping legacy datagram services that have no native encryption.
