---
name: "hashcheck"
categories:
  - blackarch-crypto
version: "2.72b0c6e"
homepage: "https://github.com/Telefonica/HashCheck"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Telefonica/HashCheck"
github-stars: 37
github-pushed: "2021-05-24"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hashcheck

> Check passwords and SHA-1 hashes against public breach data using k-anonymity, so the full secret never leaves the machine.

## What it's for

A Telefónica proof-of-concept (packaged as `hashcheck`, driven by `passme.py`) that checks whether a password has appeared in known breaches via the Have I Been Pwned Pwned Passwords API. It uses the k-anonymity method: the password is SHA-1 hashed locally and only the first five characters of the digest are sent to `api.pwnedpasswords.com`; the candidate range returned is compared locally, so the full password or hash is never transmitted. Use it for password-hygiene audits of candidate or recovered credentials. It does not crack hashes and does not check accounts or usernames — only password/digest breach exposure. Only the HIBP engine is implemented (an engine switch exists, but no second engine ships).

## Typical use cases

### Check a single password

```sh
hashcheck -p 'Password1'
```

Outcome: hashes the password with SHA-1 locally, queries the HIBP range API with just the 5-character prefix, and prints `It is possible that your password has been filtered.` when the digest appears in the breach set, or `Your password has not been filtered yet.` otherwise.

### Check an existing SHA-1 digest

```sh
hashcheck -h '7c4a8d09ca3762af61e59520943dc26494f8941b'
```

Outcome: same k-anonymity lookup on an already-computed 40-character SHA-1 digest without re-hashing anything; digests shorter than five characters are rejected with `Invalid Hash, please use a prefix of at least 5 characters.`

### Audit every password in a list

```sh
hashcheck -f passwords.txt
```

Outcome: reads the file line by line, printing `Password: <line>` followed by the leaked/not-filtered verdict for each entry.

### Audit a list of pre-computed hashes

```sh
hashcheck -l hashes.txt
```

Outcome: reads one SHA-1 digest per line and prints `Hash: <line>` followed by the breach verdict for each, so large credential dumps can be triaged without exposing full digests to the API.

*No corpus evidence — derived from upstream documentation.*
