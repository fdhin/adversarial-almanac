---
name: "gcrypt"
categories:
  - blackarch-crypto
version: "32.d3f9f06"
homepage: "https://gitlab.com/GasparVardanyan/gcrypt"
packaged-by: [blackarch]
blackarch-builddate: "2024-06-08"
build-system: other
license: MIT
meta-checked: "2026-11-09"
github-repo: "GasparVardanyan/gcrypt"
github-stars: 1
github-pushed: "2022-03-19"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gcrypt

> Minimal single-file C encryptor: one key operand, one action flag, randomized ciphertext by default.

## What it's for

A single-purpose symmetric encryptor for files or stdin/stdout: you pass the key on the command line and it encrypts or decrypts in one process — no configuration, key files, or prompts. Encryption is randomized by default, so encrypting the same file twice with the same key yields different ciphertexts; `-R` disables that for byte-reproducible output. Despite the name it has nothing to do with GNU libgcrypt. Two operational limits to know: the key is a command-line argument, so it sits in shell history and process listings, and input and output must be different files. For whole directories the same author ships gdir.pl, which drives gcrypt over trees.

## Typical use cases

### Encrypt and decrypt a file

```
gcrypt -e Password1 plaintext.txt ciphertext.bin
gcrypt -d Password1 ciphertext.bin restored.txt
```

Outcome: writes the encrypted form of `plaintext.txt` to `ciphertext.bin`, then restores the plaintext into `restored.txt`. Pointing input and output at the same path aborts with "input and output files can't be the same".

### Encrypt a stream over stdin/stdout

```
cat plaintext.txt | gcrypt -S -e Password1 > ciphertext.bin
gcrypt -S -d Password1 < ciphertext.bin > restored.txt
```

Outcome: `-S`, placed before the action flag, switches to pipe mode where only the key operand follows the action and the data flows through stdin/stdout — gcrypt then slots into pipelines without temporary files.

### Produce reproducible ciphertext

```
gcrypt -R -e Password1 plaintext.txt ciphertext.bin
```

Outcome: disables the per-run randomization, so the same input and key always produce a byte-identical file (documented as a backwards-compatibility switch). Confirm by running twice and comparing `sha256sum` output.

*No corpus evidence — derived from upstream documentation.*
