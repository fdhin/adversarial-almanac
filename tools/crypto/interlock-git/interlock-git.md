---
name: "interlock-git"
categories:
  - crypto
version: "20200902.r415"
homepage: "https://github.com/usbarmory/interlock"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, nixpkgs]
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "usbarmory/interlock"
github-stars: 308
github-pushed: "2026-07-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# interlock-git

> Web-based file encryption front-end for LUKS volumes, built for the USB armory.

## What it's for

INTERLOCK is a file encryption and management application developed for, but not limited to, the USB armory secure-computing platform. It runs a single Go binary that serves a web file manager on top of a LUKS-encrypted partition: a successful login unlocks the volume (via cryptsetup) and logout locks it back. Through the web interface files can be uploaded to and downloaded from the encrypted partition and individually encrypted or decrypted with OpenPGP, AES-256-CTR (PBKDF2/HMAC-SHA256) or TOTP ciphers, with optional key derivation tied to on-chip HSMs (NXP SCCv2, CAAM, DCP). A command-line mode (`-o`) performs selected operations locally without the web interface. It does not create the encrypted partition itself — the LUKS volume must exist beforehand.

## Typical use cases

### Serve the web file manager for an encrypted volume

```
interlock -c interlock.conf -b 0.0.0.0:4430
```

Outcome: starts the HTTPS JSON application server on the given address:port (default 0.0.0.0:4430) and dumps the applied configuration at startup; browsing to https://198.51.100.10:4430 shows the login prompt, where a volume name plus a valid LUKS password unlocks the partition and opens the file manager.

### Unlock or lock the LUKS volume from the command line

```
interlock -c interlock.conf -o unlock:encryptedfs
```

Outcome: prompts once for the LUKS password and unlocks the volume to the `interlockfs` device mapping without starting the web interface; `interlock -c interlock.conf -o lock` closes it again. With HSM support configured, the password is filtered through the HSM before use.

### Derive an HSM key locally

```
interlock -c interlock.conf -o derive:12ef
```

Outcome: performs HSM key derivation from the hex diversifier given after `derive:`; running plain `-o derive` prompts twice for a password and derives from it instead. Intended for scripting encryption on embedded firmware without the web interface.

*No corpus evidence — derived from upstream documentation.*
