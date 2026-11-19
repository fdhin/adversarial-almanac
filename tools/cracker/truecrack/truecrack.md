---
name: "truecrack"
categories:
  - blackarch-cracker
version: "35"
homepage: "https://github.com/lvaccaro/truecrack"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2014-05-29"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "lvaccaro/truecrack"
github-stars: 167
github-pushed: "2024-05-16"
github-archived: false
github-issues: 36
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# truecrack

> CUDA-optimized password cracker for TrueCrypt volumes, with a CPU fallback.

## What it's for

Brute-forces the password of a TrueCrypt container or partition. It implements TrueCrypt's full KDF and cipher stack — PBKDF2 with RIPEMD-160 (default), SHA-512 or Whirlpool, XTS mode with AES, Serpent or Twofish — including hidden volumes and embedded backup headers, and attacks by dictionary or by generated alphabets. It belongs to the 2012-era TrueCrypt toolchain and is optimized for NVIDIA CUDA boards (a CPU mode exists at build time); modern hashcat covers the same volumes, so this tool mainly matters on legacy GPU rigs or where its checkpoint/restore behavior is wanted. It only recovers volume passwords — it does not mount, inspect or repair volumes.

## Typical use cases

### Dictionary attack with defaults

Try a wordlist against a container using RIPEMD-160 + AES XTS, the TrueCrypt defaults.

```
truecrack -t volume.tc -w passwords.txt
```

Outcome: streams the wordlist through key derivation on the GPU; prints the recovered password when a candidate decrypts the volume header, otherwise exhausts the list and exits.

### Match a non-default KDF and cipher

Attack a volume created with SHA-512 and Serpent instead of the defaults.

```
truecrack -t volume.tc -w passwords.txt -k sha512 -e serpent
```

Outcome: the same dictionary attack computed with the selected PBKDF2 PRF and XTS cipher — candidates only succeed when both match how the volume was created.

### Pure alphabet attack

Generate candidates from a charset instead of a wordlist.

```
truecrack -t volume.tc -c abcdefghijklmnopqrstuvwxyz0123456789 -s 8 -m 12
```

Outcome: enumerates every 8- to 12-character string over the given alphabet (optionally after a `-p` prefix), stopping at the first password that opens the volume.

### Attack the backup header or a hidden volume

Recover passwords when the standard header is damaged or wiped, or for inner hidden volumes.

```
truecrack -t volume.tc -w passwords.txt -b
truecrack -t volume.tc -w passwords.txt -H
```

Outcome: KDF trials are validated against the embedded backup header (`-b`) or the hidden-volume header (`-H`) instead of the primary header.

### Resume an interrupted run

Continue a long attack from where it stopped.

```
truecrack -t volume.tc -w passwords.txt -r 5400000
```

Outcome: resumes the dictionary attack from the given position (here word 5,400,000), skipping candidates already tried in the previous run.

*No corpus evidence — derived from upstream documentation.*
