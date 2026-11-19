---
name: "pkcrack"
categories:
  - blackarch-cracker
version: "1.2.3"
homepage: "https://www.unix-ag.uni-kl.de/~conrad/krypto/pkcrack/download1.html"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-05-04"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "keyunluo/pkcrack"
github-stars: 213
github-pushed: "2021-10-30"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pkcrack

> Known-plaintext cracker for the legacy PkZip (ZipCrypto) encryption.

## What it's for

pkcrack implements the Biham/Kocher known-plaintext attack against archives encrypted with the traditional PkZip cipher (ZipCrypto, the "classic" zip password encryption). It does not brute-force passwords: given roughly 12+ bytes (100 or more for a fast run) of a file that also exists inside the encrypted archive — compressed the same way — it recovers the archive's internal encryption keys and decrypts every member, usually within minutes to about an hour. This makes it the tool of choice whenever any file inside an encrypted zip is guessable or obtainable elsewhere (a default config file, a vendored library, a README). It only attacks ZipCrypto: AES-encrypted zip members cannot be broken this way (a truncated/corrupted-archive cryptanalysis like bkcrack's is a separate approach, and AES zips are simply out of scope).

## Typical use cases

### Decrypt a ZipCrypto archive from a known plaintext file

Recover the keys and produce a fully decrypted archive when you hold an unencrypted copy of one member, compressed identically.

```
pkcrack -C encrypted.zip -c secret.txt -P plain.zip -p secret.txt -d cracked.zip -a
```

Outcome: after the two cracking stages (typically minutes to an hour depending on plaintext size) pkcrack prints the recovered `key0/key1/key2`, then decrypts every member and writes a plain archive to `cracked.zip` that any unzip tool can open. CRC errors on unzip mean the plaintext was wrong or compressed differently — retry with another compression method.

### Recover keys from bare ciphertext and plaintext files

Work without whole archives when you have raw (headerless) encrypted data and matching plaintext.

```
pkcrack -c cipherfile.enc -p plainfile
```

Outcome: prints the recovered `key0`, `key1`, `key2` triple rather than a decrypted archive (no `-d` is possible without `-C`); the ciphertext file must be exactly 12 bytes longer than the plaintext because of PkZip's prepended random bytes. Feed the keys to `zipdecrypt` to decrypt the original archive.

### Pull a raw member out of an archive with extract

Prepare the `-c`/`-p` inputs by dumping a single archive member with its ZIP headers stripped.

```
extract encrypted.zip config.ini
```

Outcome: writes the member's raw (still-encrypted, still-compressed) data to `config.ini` in the current directory — exactly the headerless form pkcrack's `-c` option expects; create any subdirectories the member path needs beforehand.

### Decrypt an archive with recovered keys via zipdecrypt

Skip the (much slower) password search when keys are already known.

```
zipdecrypt key0 key1 key2 encrypted.zip decrypted.zip
```

Outcome: writes `decrypted.zip` with every member decrypted using the given key triple; this is what to do when pkcrack found multiple candidate keys and `-d` picked the wrong one.
