---
name: "crackpkcs12"
categories:
  - blackarch-cracker
version: "0.2.10.11.c7dedb1"
homepage: "https://github.com/crackpkcs12/crackpkcs12"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-12-12"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "crackpkcs12/crackpkcs12"
github-stars: 162
github-pushed: "2019-04-26"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crackpkcs12

> Multithreaded dictionary and brute-force cracker for PKCS#12 (.pfx/.p12) certificate file passwords.

## What it's for

Audits the password protecting PKCS#12 containers — the `.pfx`/`.p12` files that bundle a certificate with its private key, routinely exported by applications and appliances for TLS client authentication. crackpkcs12 recovers that password offline using OpenSSL, running dictionary attacks, brute force, or a dictionary pass that hands over to brute force automatically, spread across multiple threads. Typical situation: a password-protected `.pfx` is obtained during an assessment and the key material inside stays unreachable until the container password is known.

## Typical use cases

### Run a dictionary attack against a protected certificate

Test a wordlist against the container password first.

```
crackpkcs12 -d /usr/share/wordlists/rockyou.txt cert.pfx
```

Outcome: tries each word as the PKCS#12 password across all threads and prints the password on a match; an exhausted wordlist ends without a credential.

### Brute-force the password

Fall back to exhaustive search when wordlists fail.

```
crackpkcs12 -b cert.pfx
```

Outcome: brute-forces candidate passwords over the default charset using all available threads until the password is found or the attack is stopped.

### Chain dictionary then brute force with a restricted charset

Combine both attacks and constrain the keyspace.

```
crackpkcs12 -d /usr/share/wordlists/rockyou.txt -b -caA -m5 cert.pfx
```

Outcome: runs the dictionary attack first, then automatically starts brute force using only lower- and upper-case letters with a minimum password length of 5.
