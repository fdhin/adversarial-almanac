---
name: "pemcrack"
categories:
  - blackarch-cracker
version: "12.66e02b8"
homepage: "https://github.com/robertdavidgraham/pemcrack"
packaged-by: [blackarch]
blackarch-builddate: "2023-03-01"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "robertdavidgraham/pemcrack"
github-stars: 313
github-pushed: "2021-11-17"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pemcrack

> Recovers the passphrase of an encrypted PEM private key by dictionary attack (or a tiny brute force) through OpenSSL's `PEM_read_PrivateKey` (Robert Graham).

*No corpus evidence — derived from upstream documentation and the packaged source; the example output shape was verified by building the tool and running it against its own test pair.*

## What it's for

You have an encrypted PEM file — an SSL/TLS private key — and lost its passphrase. pemcrack reads the PEM and tries candidate passphrases via OpenSSL's high-level key API: either every line of a wordlist you give it, or, with no wordlist at all, a built-in brute force over very short candidates (up to about six characters) — so the dictionary mode is the practical one. It is a small single-threaded C program that its own author describes as "extraordinarily slow": fine for a quick wordlist run over a key or two, not for a serious key search (John the Ripper with its PEM/SSH formatters is the heavy-duty alternative). The interface is exactly two arguments: the PEM file, and optionally the wordlist.

## Typical use cases

### Dictionary attack on an encrypted PEM key

```
pemcrack key.pem wordlist.txt
```

Outcome: prints a banner and progress lines (`-> candidate`) on stderr while working; on success prints `found: <passphrase>` and exits 0 — verified live, the shipped test pair yields `found: komodia` — and prints `NOT FOUND` when the wordlist is exhausted.

### Let it brute force very short passphrases

```
pemcrack key.pem
```

Outcome: warns "no dictionary specified, so brute-forcing..." and enumerates short candidates (single characters up to six) against the key — worth trying only for trivial passphrases or as a quick sanity check before a bigger tool.
