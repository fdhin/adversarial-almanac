---
name: "encryptpad"
categories:
  - crypto
version: "0.5.0.3-1parrot1"
homepage: "https://github.com/evpo/EncryptPad"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, aur]
build-system: python
meta-checked: "2026-11-09"
github-repo: "evpo/EncryptPad"
github-stars: 478
github-pushed: "2026-10-18"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 19
gh-activity-checked: "2026-11-09"
---

# encryptpad

> Minimalist editor and binary encryptor using symmetric OpenPGP (RFC 4880).

## What it's for

EncryptPad is a Qt application for creating and editing symmetrically encrypted text, and for encrypting arbitrary binary files, with a companion command-line tool `encryptcli` for scripting. Its focus is symmetric OpenPGP: files are protected by a passphrase, a key file, or both, and written in the standard OpenPGP format that GnuPG and PGP software can also open — unlike suites built around key pairs. Use it for standalone encrypted notes or file protection where recipients only share a secret; it is not a mail client or a key-management suite.

## Typical use cases

### Generate a key file protected by a passphrase

```
echo -n "key" | encryptcli --generate-key --key-pwd-fd 0 my_key.key
```

Outcome: writes `my_key.key` containing a random 64-byte sequence (size adjustable with `--key-file-length`); the passphrase, read from file descriptor 0, protects use of the key file itself.

### Encrypt a file with a key file only

```
cat plain_text.txt | encryptcli -e --key-file my_key.key --key-only --key-pwd-fd 3 -o plain_text.txt.gpg 3< <(echo -n "key")
```

Outcome: writes `plain_text.txt.gpg`, an OpenPGP-format file unlocked by the key file alone (`--key-only` suppresses the passphrase prompt); the key passphrase arrives on descriptor 3.

### Decrypt a file

```
encryptcli -d --key-file my_key.key plain_text.txt.gpg
```

Outcome: prompts for the key file passphrase and writes the decrypted plaintext to stdout (or to a file with `-o`); add `--armor` when encrypting to emit ASCII-armored output instead of binary.

### Pick the cipher and compression

```
encryptcli -e --cipher-algo TWOFISH --compress-algo NONE -o secret.enc secret.txt
```

Outcome: encrypts `secret.txt` (positional input file; stdin when omitted) to `secret.enc` using Twofish with compression disabled — defaults are AES256 with ZLIB.

### Work interactively in the GUI

```
encryptpad
```

Outcome: opens the editor, where files are saved encrypted with a passphrase and/or key file in OpenPGP format; `--lang`, `--log-file` and `--log-severity` control GUI localization and diagnostics.

*No corpus evidence — derived from upstream documentation.*
