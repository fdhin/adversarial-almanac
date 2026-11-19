---
name: "tomb"
categories:
  - crypto
version: "2.9"
homepage: "https://www.dyne.org/software/tomb/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur, nixpkgs]
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "dyne/tomb"
github-stars: 1600
github-pushed: "2025-12-16"
github-archived: false
github-issues: 36
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# tomb

> The Linux "Crypto Undertaker" — LUKS-encrypted storage you create, open, and close as a single file, unlocked by a key file kept separately.

## What it's for

Tomb is a minimalistic zsh command-line tool over dm-crypt and LUKS that manages encrypted volumes as ordinary files: a `.tomb` file is the encrypted volume, and a separate `.tomb.key` file — itself GPG-encrypted with a passphrase, optionally hidden inside an image (steganography) or protected as a second factor with a FIDO2 passkey — unlocks it. Because the key lives apart from the volume, a tomb can sit on cloud storage or a shared disk while the key stays with you; `tomb open` mounts the decrypted contents as a directory and `tomb close` (or `slam`) detaches it again. It handles file/volume encryption workflow only — not full-disk encryption — and builds on standard Linux crypto infrastructure (cryptsetup, LUKS), in active development since 2007.

## Typical use cases

### Create a new encrypted tomb with its key

Allocate, forge, and lock a fresh encrypted volume.

```
tomb dig -s 120 secret.tomb
tomb forge -k secret.tomb.key
tomb lock -k secret.tomb.key secret.tomb
```

Outcome: digs a 120 MiB sparse volume file (secret.tomb), forges secret.tomb.key protected by a chosen passphrase, and then formats and locks the volume with that key — after the dig/forge/lock sequence, secret.tomb is a ready-to-open encrypted container.

### Open a tomb and work inside it

Unlock and mount the volume for normal use.

```
tomb open -k secret.tomb.key secret.tomb
```

Outcome: prompts for the key's passphrase, opens the LUKS volume, and mounts its decrypted contents as a writable directory; files stored there are transparently encrypted in the underlying secret.tomb file.

### Close a tomb, or slam it shut when busy

Detach the volume cleanly or force it closed.

```
tomb close
tomb slam
```

Outcome: `close` detaches and locks the tomb, refusing if processes still hold files open inside it; `slam` kills all processes with open files in the tomb and then closes it anyway — the way out when a stray program keeps the volume busy.

*No corpus evidence — derived from upstream documentation.*
