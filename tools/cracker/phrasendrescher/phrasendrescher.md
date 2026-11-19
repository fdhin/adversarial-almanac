---
name: "phrasendrescher"
categories:
  - blackarch-cracker
version: "1.2.2c"
homepage: "http://www.leidecker.info/projects/phrasendrescher/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2019-05-12"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "hemp3l/phrasendrescher"
github-stars: 0
github-pushed: "2019-04-05"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# phrasendrescher

> phrasen|drescher (p|d) — a modular, multi-processing passphrase cracking framework (Nico Leidecker): one candidate generator, plugins for the target — RSA/DSA key passphrases, MS SQL hashes, online SSH accounts, HTTP form logins, OpenPGP-encrypted files.

*No corpus evidence — derived from upstream documentation. The canonical source is the author's site (frontmatter `homepage`, still live); the linked GitHub repo is a third-party mirror carrying the same release and author.*

## What it's for

One generator, many targets. The p|d core produces candidate passphrases either from a wordlist — optionally rewriting each word with rules (case flips, leet-speak, appending/prepending digits) — or incrementally over a custom character map (the `PD_CHARMAP` environment variable), and hands every candidate to a plugin that knows how to test it. Shipped plugins: `pkey` (RSA/DSA PEM key passphrases, single file or directory), `mssql` (MS SQL 2000/2005 salted SHA1 hashes), `ssh` (online attacks against SSH2 services: password, keyboard-interactive or public key), `http-raw` (HTTP form logins via a raw request template with a `{PASSWORD}` placeholder) and `enc-file` (symmetric passphrases of GnuPG/PGP-encrypted files via gpgme). Work spreads over multiple threads with `-w`. The upstream binary is `pd` (run `pd -h` to see the plugin directory, override it with `PD_PLUGINS`; running a bare plugin name prints its own options); the package installs it as `phrasendrescher` because `pd` is taken by another package — substitute that name in the examples if you run the packaged build.

## Typical use cases

### Dictionary attack on a PEM key with rewriting rules

```
pd pkey -K key.pem -d wordlist.txt -r Ad
```

Outcome: tries every wordlist entry plus rewrites — `A` upper-cases the word, `d` appends each digit 0–9 — as candidate passphrases for the encrypted key (pass a directory to `-K` to sweep many keys); rewritten duplicates of the original word are discarded.

### Incremental brute force over a chosen alphabet

```
PD_CHARMAP="abcdefghijklmnopqrstuvwxyz0123456789" pd pkey -K key.pem -i 6:8
```

Outcome: generates every 6-to-8-character passphrase from the given map — default is all human-readable characters — and tests them in map order against the key; reversing the string iterates backwards, useful when password policy dictates the shape.

### Crack an MS SQL 2000/2005 password hash

```
pd mssql -H 0x01006d75a401ba0a8bfc2beab5b86efc930300d1a2561a783aac
```

Outcome: attacks the salted-SHA1 MS SQL hash with the configured generation mode (combine with `-d` or `-i` like any other plugin) and reports the plaintext on a hit.

### Online SSH account testing with several workers

```
pd ssh -U user -P 2222 -t target.example.org -d wordlist.txt -w 8
```

Outcome: eight worker threads try wordlist passwords against the SSH2 service at target.example.org:2222 (defaults are user `root`, port 22); `-T hosts.txt` sweeps a file of targets instead, and `-k` switches to public-key authentication.

### Brute force a web login through a raw request template

```
pd http-raw -R request.txt -m "Login failed"
```

Outcome: replays the raw HTTP request from request.txt, substituting each candidate for the `{PASSWORD}` placeholder; responses that do not contain the marker "Login failed" are reported as successful logins. No SSL support — tunnel through stunnel for HTTPS targets.
