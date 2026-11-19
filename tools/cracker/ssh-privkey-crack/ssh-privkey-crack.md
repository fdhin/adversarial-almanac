---
name: "ssh-privkey-crack"
categories:
  - blackarch-cracker
version: "0.4"
homepage: "https://code.google.com/p/lusas/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ssh-privkey-crack

> Offline recovery of an encrypted SSH private key's passphrase from a stream of candidate words.

## What it's for

Recovers the passphrase of an encrypted RSA or DSA SSH private key in OpenSSL PEM format when you have the key file but not the passphrase. It works entirely offline against the key file itself — no SSH server is contacted — and reads one candidate passphrase per line from stdin, so any generator (a plain wordlist file, John the Ripper's incremental mode) can drive it. It only understands traditional PEM keys, not the newer OpenSSH private-key format, and it cannot do anything with a public key. Written in C against OpenSSL (original by anonymous@echo.or.id, enhanced by michu@neophob.com) and preserved in the "lusas" security-auditing script collection.

## Typical use cases

### Test a wordlist against a key

Feed a dictionary as stdin and point the tool at the encrypted key.

```
ssh-privkey-crack id_rsa < wordlist.txt
```

Outcome: each line is tried as the key's passphrase; wrong candidates are skipped silently, and on a match the tool prints a `Passphrase match: <password>. Found password after N seconds and N tries.` banner and exits.

### Drive candidates from John the Ripper

Use John's generator instead of a static list.

```
john --stdout --incremental | ssh-privkey-crack id_dsa
```

Outcome: John emits incremental-mode candidates (typically thousands per second) and each is tried against the key; on success the pipeline ends with the passphrase banner and exit code 0.

Both examples accept `-v` as a second argument to also print the recovered key's type and parameters (RSA/DSA dump), or `-q` for quiet mode.

*No corpus evidence — derived from upstream documentation.*
