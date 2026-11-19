---
name: "bkcrack"
categories:
  - blackarch-cracker
version: "v1.8.1.r53.g980b9ad"
homepage: "https://github.com/kimci86/bkcrack"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-09-28"
build-system: cmake
license: Zlib
meta-checked: "2026-11-09"
github-repo: "kimci86/bkcrack"
github-stars: 2239
github-pushed: "2026-09-26"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 46
gh-activity-checked: "2026-11-09"
---

# bkcrack

> Breaks legacy ZipCrypto archives with the Biham–Kocher known-plaintext attack, then unlocks, re-encrypts, or recovers the password.

## What it's for

Entries encrypted with traditional PKWARE Zip encryption (ZipCrypto) — not AES — can be opened without the password when at least 12 bytes of one entry's plaintext are known, 8 of them contiguous; a common source is a file you also hold uncompressed, such as a dotfile or a known file header. bkcrack runs the known-plaintext attack to recover the three 32-bit internal keys of the keystream generator, and from those keys it can decipher entries, produce a password-free copy of the archive, re-encrypt it with a password of your choice, or search for the original password by brute force or mask. The key-recovery step takes minutes to hours depending on how much contiguous plaintext is available. AES-encrypted zip entries are out of scope.

## Typical use cases

### Confirm which entries are attackable

```
bkcrack -L legacy.zip
```

Outcome: lists every entry with its metadata and encryption; entries shown with ZipCrypto are vulnerable to the known-plaintext attack, AES-encrypted entries are not.

### Recover the internal keys from a plaintext copy

```
bkcrack -C encrypted.zip -c report.doc -P plain.zip -p report.doc
```

Outcome: aligns the encrypted entry with the same file stored unencrypted in plain.zip and runs the attack; on success prints the keys as `Keys: 12345678 23456789 34567890`. Save them — every later step needs only the keys.

### Write a password-free copy of the archive

```
bkcrack -C encrypted.zip -k 12345678 23456789 34567890 -D unlocked.zip
```

Outcome: produces unlocked.zip with the same entries and encryption removed, assuming every entry originally shared one password.

### Re-lock the archive with a chosen password

```
bkcrack -C encrypted.zip -k 12345678 23456789 34567890 -U unlocked.zip new_password
```

Outcome: produces unlocked.zip with identical content encrypted under new_password, openable in any zip tool without knowing the original password.

### Recover the original password itself

```
bkcrack -k 12345678 23456789 34567890 -b ?p -l 8..10
```

Outcome: brute-forces passwords of 8 to 10 printable characters derived from the keys and prints any match; feasible because recovering the password from the keys costs roughly n^(l-6) tries.

### Narrow the search with a password mask

```
bkcrack -k 12345678 23456789 34567890 -m ?l?l?l?l?l?l?l?l-?d?d?d?d?d?d
```

Outcome: searches only candidates matching the mask (here 8 lowercase letters, a hyphen, 6 digits), turning an hours-long brute force into a near-instant run; predefined charsets are `?l ?u ?d ?s ?a ?p ?b` and custom sets are defined with `-s`.
