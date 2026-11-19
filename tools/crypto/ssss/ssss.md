---
name: "ssss"
categories:
  - crypto
version: "0.5"
homepage: "http://point-at-infinity.org/ssss/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur, nixpkgs]
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "MrJoy/ssss"
github-stars: 104
github-pushed: "2026-02-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# ssss

> Shamir's Secret Sharing on the command line — split a secret into N shares, any threshold T of which recombine it, by B. Poettering.

*No corpus evidence — derived from upstream documentation.*

*GitHub: `MrJoy/ssss` — a maintained fork of B. Poettering's original ("Fork of B. Poettering's SSSS", 0.5.1+ patches); the AUR package builds its signed tags.*

## What it's for

ssss is a UNIX implementation (2006, B. Poettering, linked against libgmp) of Adi Shamir's 1979 (t,n)-threshold scheme. It turns one secret into n shares such that any t of them reconstruct the secret, while fewer than t reveal nothing at all — provably: with t-1 shares an attacker can do no better than guessing. That is the property passwords-in-an-address-book style schemes lack, and it is what you want for splitting a recovery key across locations, custodians or channels: stealing some shares gains nothing until the threshold is met. Two programs do all the work — `ssss-split` (prompt for a secret, generate shares) and `ssss-combine` (read shares, print the secret); both are interactive, drawing randomness from `/dev/random`, and the secret is limited to 128 ASCII characters (hex mode for binary data). For anything larger, apply the documented hybrid pattern: encrypt the payload with a block cipher (openssl, gpg) and secret-share only the key. It is information-theoretic splitting, not encryption with an expiry or revocation — once shares exist, destroying the threshold is the only revocation.

## Typical use cases

### Split a secret into shares

```
ssss-split -t 3 -n 5
```

Outcome: prompts for the secret (at most 128 ASCII characters), picks a security level to fit it, and prints five numbered shares (`1-…` through `5-…`). Distribute them; any 3 of the 5 will rebuild the secret, and any 2 alone are useless by design.

### Reconstruct the secret from shares

```
ssss-combine -t 3
```

Outcome: prompts for any 3 shares (order does not matter), then prints the recovered secret (`Resulting secret: …`). Enter fewer than the threshold and reconstruction is impossible — that is the scheme's guarantee, not a tool limitation.

### Label shares belonging to different secrets

```
ssss-split -t 3 -n 10 -w vault-key
```

Outcome: every generated share is prefixed with the token `vault-key`, so share sets protecting several independent secrets cannot be confused when they end up in the same safe, repo or envelope.

### Share binary keys in hex mode at a fixed level

```
ssss-split -t 2 -n 4 -x -s 256
```

Outcome: hexadecimal digits are used for I/O instead of ASCII characters, which suits binary secrets such as block-cipher keys; `-s 256` fixes the security level in bits (multiples of 8, up to 1024, `0` for automatic), which sets the share length and pads shorter secrets. Combine again with `ssss-combine -t 2 -x`.
