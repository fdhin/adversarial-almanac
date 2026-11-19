---
name: "salsapipe-git"
categories:
  - tunnel
version: "20150727.r26.6d81ace"
homepage: "https://github.com/prurigro/salsapipe"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur]
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "prurigro/salsapipe"
github-stars: 2
github-pushed: "2016-05-31"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# salsapipe-git

> Salsa20-encrypted peer-to-peer channel — session keys are exchanged under GPG public-key encryption, then traffic is symmetrically encrypted in and out through file descriptors. The package ships the reference client `salsamsg`.

## What it's for

salsapipe (Kevin MacMartin) provides authenticated, encrypted messaging between two peers over an otherwise plain TCP connection. Each side needs a GPG keypair and the peer's public key in its keyring; on connect, fresh Salsa20 session keys are exchanged, each encrypted to the other side's public key, after which the channel is symmetrically encrypted. When the Salsa20 nonce space has been walked through, a new session key is negotiated automatically, so no nonce or key is ever reused — a property most one-shot encrypted-pipe tools lack. The library's core is fd-based (it reads and writes whatever file descriptors it is handed, so other programs can be wired onto it); the packaged `salsamsg` binary is the simple interactive client demonstrating the protocol (documented in `salsamsg(1)`). The actual message-length field is transmitted in the clear — a known information leak the author notes in the README.

## Typical use cases

### Run the encrypted listener

```
salsamsg -m l -i 0.0.0.0 -p 9000 -l LOCAL_PGP_ID -r REMOTE_PGP_ID
```

Outcome: binds 0.0.0.0:9000 and waits for the peer; when the peer connects, both sides exchange GPG-wrapped Salsa20 session keys and the encrypted session is up. Replace the ID placeholders with your own and the peer's long-form GPG key IDs (`gpg --list-keys --keyid-format long`).

### Connect as the client

```
salsamsg -m c -i 198.51.100.10 -p 9000 -l LOCAL_PGP_ID -r REMOTE_PGP_ID
```

Outcome: connects out to the listener at 198.51.100.10:9000, performs the same key exchange, and relays what is written to its input fd encrypted to the peer; lines typed on one side appear decrypted on the other, and both sides transparently rotate to a new session key when the nonce permutations are exhausted.

*No corpus evidence — derived from upstream documentation.*
