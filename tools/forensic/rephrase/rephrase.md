---
name: "rephrase"
categories:
  - forensics
version: "0.2-4"
homepage: "https://www.roguedaemon.net/rephrase/"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur]
build-system: other
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rephrase

> Recover a GnuPG or LUKS passphrase you can almost remember, by trying every combination of your alternatives.

## What it's for

Rephrase (by Phil Lanch) is a specialized passphrase recovery tool for GnuPG secret keys, gpg-symmetrically-encrypted files, and Cryptsetup/LUKS volumes. It is not a cracker: you describe the passphrase you nearly remember as a pattern of alternatives — e.g. `(s|S)up(e|3)r(-| )secret` — and it tries every combination against the real key, keeping the pattern and its guesses in secure memory and piping each candidate to a child gpg/cryptsetup process, never writing candidate lists to disk. It is deliberately slow but safe to run for a long time (constant memory); brute-forcing large candidate spaces is out of scope. When a combination matches, it does not print the passphrase — it prints the index numbers of the alternatives that worked so you can reconstruct it privately.

## Typical use cases

### Recover the passphrase of a GnuPG secret key

Identify the key the way gpg does and describe what you remember.

```
rephrase --gpg-key user@example.com
```

Outcome: prompts (without echoing) for a pattern like `(p|P)(a|@)(s|5)(s|5)w0(r|R)d`; tries each expansion against the secret key and, on success, prints the matching alternative indexes (e.g. `1 2 2 1 1 2`) rather than the passphrase itself.

### Recover the passphrase of a symmetrically encrypted file

For files encrypted with `gpg --symmetric`.

```
rephrase --gpg-symmetric secret.gpg
```

Outcome: same interactive pattern prompt; every combination is tested by decrypting the file with a child gpg process until one succeeds, again reported as index numbers.

### Recover the passphrase of a LUKS volume

For Cryptsetup/LUKS containers when the passphrase is almost known.

```
rephrase --luks /dev/mapper/backup
```

Outcome: prompts for the pattern and tests candidates against the LUKS header via cryptsetup (much slower per attempt than GnuPG); a successful combination is reported as index numbers for you to decode.

*No corpus evidence — derived from upstream documentation.*
