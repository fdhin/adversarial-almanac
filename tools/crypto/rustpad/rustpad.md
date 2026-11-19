---
name: "rustpad"
categories:
  - blackarch-crypto
version: "v1.8.1.r1.g11ce343"
homepage: "https://github.com/Kibouo/rustpad"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-03-18"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Kibouo/rustpad"
github-stars: 100
github-pushed: "2023-03-05"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rustpad

> Multi-threaded padding-oracle attack tool in Rust — decrypt ciphertexts and forge chosen-plaintext ciphertexts against a web or script oracle, without the key, from an interactive TUI.

*No corpus evidence — derived from upstream documentation.*

## What it's for

rustpad exploits a padding oracle: any service that reacts differently to valid versus invalid PKCS#7 padding in a submitted ciphertext. Given the block size and one original ciphertext, it decrypts the ciphertext (or encrypts arbitrary plaintext to a valid ciphertext) without ever knowing the encryption key — a multi-threaded successor to padbuster. You point it at an oracle in one of two modes: `web` for an HTTP endpoint whose URL or POST body contains the ciphertext (rustpad auto-calibrates which responses mean "padding correct"), or `script` for anything you can wrap in a shell script that exits 0 on correct padding. It detects ciphertext encoding (hex, base64, base64url) automatically, caches solved blocks, and shows progress in a real-time TUI that also works without a TTY so output can be piped to a file. It cannot help without an oracle — a service must leak padding validity through timing, status codes, or content for the attack to exist — and it needs at least one known ciphertext to work from.

## Typical use cases

### Decrypt a ciphertext through a web oracle

```
rustpad web --oracle 'https://target.example.com/profile?token=CTEXT' --block-size 16 --decrypt '5pqftEB4vG8IsM9T3ZnY2Q=='
```

Outcome: the TUI opens, rustpad calibrates the oracle's response to correct versus incorrect padding, then decrypts block by block and byte by byte with parallel requests — every occurrence of the `CTEXT` keyword in the URL is replaced with the forged ciphertext (keyword configurable via `--keyword`). The recovered plaintext appears in the TUI and in the info log; add `--output rustpad.log` to capture it to a file.

### Forge a ciphertext for chosen plaintext

```
rustpad web --oracle 'https://target.example.com/profile?token=CTEXT' --block-size 16 --decrypt '5pqftEB4vG8IsM9T3ZnY2Q==' --encrypt 'role=admin'
```

Outcome: encryption mode still needs a known ciphertext to gather necessary data, but instead of decrypting it produces a new ciphertext that the service will decrypt to `role=admin` — handy for privilege-escalation-style parameter tampering when the app trusts an encrypted field.

### Attack a non-HTTP oracle through a wrapper script

```
rustpad script --oracle ./oracle.sh --block-size 8 --decrypt '8a1f4c...'
```

Outcome: the script is invoked with each forged ciphertext as its first argument and must exit 0 when the padding is correct (any other exit code means incorrect) — this wraps local binaries, custom protocols or exotic services the web mode cannot speak to. In the TUI, decryption proceeds exactly as with a web oracle.

### Tune threads, delay and encoding

```
rustpad web --oracle 'https://target.example.com/?q=CTEXT' --block-size 16 --decrypt '5pqftEB4...' --threads 16 --delay 100 --encoding hex
```

Outcome: `--threads` bounds the thread pool and `--delay` inserts a pause (milliseconds) between requests within a thread to keep a fragile target alive, while `--encoding hex` overrides the automatic hex/base64/base64url detection of the ciphertext's encoding. Use `--no-iv` when the ciphertext excludes the initialisation vector, and `--no-cache` to disable the on-disk block cache.
