---
name: "codecrypt"
categories:
  - crypto
version: "1.8"
homepage: "https://github.com/exaexa/codecrypt"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur]
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "exaexa/codecrypt"
github-stars: 324
github-pushed: "2022-09-25"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# codecrypt

> GnuPG-style encryption and signing tool whose algorithms are all quantum-computer-resistant (binary `ccr`).

## What it's for

codecrypt is an offline, GnuPG-like program for public-key encryption and digital signatures built exclusively on post-quantum primitives: quasi-cyclic MDPC McEliece for encryption and hash-based Merkle-tree (FMTSeq) signatures, with parameter sets named after their hash and stream cipher (e.g. `MCEQCMDPC128FO-CUBE256-CHACHA20`). It offers keyrings, ASCII armor, recipient-based encryption, symmetric mode driven by a key file, and signed hashfiles for large data. Two author caveats: the project is currently unmaintained, and the cryptography is not intended for "online" use because some primitives (MDPC decoding in particular) leak timing information.

## Typical use cases

### Generate keypairs and inspect the keyring

```
ccr -g help
ccr -g sig --name "user"
ccr -g enc --name "user"
ccr -k
```

Outcome: `-g help` lists the available algorithm specifications (the reference for names accepted by `-g`); the next two commands create one signature key and one encryption key for `user`; `ccr -k` lists the public keyring and `ccr -K` the secret one.

### Exchange public keys

```
ccr -p -a -o my_pubkeys.asc -F user
ccr -inaf < friends_pubkeys.asc
ccr -ia -R friends_pubkeys.asc --name "user@example.com"
```

Outcome: exports ASCII-armored public keys matching the name filter to `my_pubkeys.asc`; `-inaf` previews an incoming key file with fingerprints but imports nothing; `-ia` imports it, applying the new name.

### Sign and encrypt a message, then verify and decrypt a reply

```
ccr -se -r Frank < message.txt > message.ccr
ccr -dv -o reply.txt < reply.ccr
```

Outcome: produces a combined signed-and-encrypted message for recipient `Frank` (selectable by name or `@keyid`); the second command checks the sender's signature and writes the decrypted plaintext to `reply.txt`.

### Encrypt a large file with a symmetric key

```
ccr -g sha256,chacha20 -aS symkey.asc
ccr -eaS symkey.asc -R big_data.iso -o big_data_encrypted.iso
ccr -daS symkey.asc < big_data_encrypted.iso > big_data.iso
```

Outcome: generates an ASCII-armored symmetric key for the chosen `hash,cipher` pair, encrypts the file with it (the key is a file operand, not a password prompt), and decrypts it back.

### Sign a large file as a hashfile

```
ccr -sS hashfile.ccr < big_data.iso
ccr -vS hashfile.ccr < big_data.iso
```

Outcome: writes a small signed hash of the file instead of a full signature; re-running with `-vS` over the same file reports a match, and over a modified file fails verification.

### Password-protect private keys

```
ccr -L
ccr -U
```

Outcome: locks all secret keys behind a password-derived symmetric key (prompted) and unlocks them again; `-w @sha256,chacha20` selects the derivation and cipher used for locking.

*No corpus evidence — derived from upstream documentation.*
