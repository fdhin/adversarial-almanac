---
name: "jwtcat"
categories:
  - blackarch-cracker
version: "77.f80f3d9"
homepage: "https://github.com/aress31/jwtcat"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "aress31/jwtcat"
github-stars: 333
github-pushed: "2024-05-03"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jwtcat

> CPU-based JWT secret cracker and alg=none vulnerability checker.

## What it's for

jwtcat is a Python 3 script that attacks JSON Web Tokens signed with HS256. It runs guessing attacks against the HMAC secret — brute-force over a custom charset or a wordlist — and tests tokens for the classic `alg=none` signature-bypass vulnerability (CVE-2015-2951). Recovered secrets are printed and recorded in a potfile (`jwtpot.potfile` by default), hashcat-style. It only handles symmetric HS256 secrets and the none-alg check; it does not forge tokens, attack asymmetric algorithms, or talk to applications.

## Typical use cases

### Dictionary-attack the HS256 secret

Test a wordlist of likely secrets against the token's signature.

```
python3 jwtcat.py wordlist eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U -w jwt.secrets.list
```

Outcome: tries each wordlist entry as the HMAC key with a progress bar; a hit logs the recovered secret and appends it to the potfile. Add `-o cracked.txt` to also write recovered keys to a dedicated file.

### Brute-force the secret over a charset

Exhaustively generate candidates from a defined alphabet and length range.

```
python3 jwtcat.py brute-force eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U -c abcdefghijklmnopqrstuvwxyz0123456789 --increment-min 1 --increment-max 4
```

Outcome: increments candidate length from min to max, testing every combination of the charset as the signing key; short weak secrets are recovered in minutes, and success is logged/potted the same way. Expect this to be slow beyond ~5 characters — it is single-process CPU work.

### Check a token for the alg=none bypass

```
python3 jwtcat.py vulnerable eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIn0.dozjgNryP4J3jVmNHl0w5N_XgL0n3I9PlFUP0THsR8U
```

Outcome: inspects the token and reports whether it is accepted with the `none` algorithm (signature stripped) and confirms the signing algorithm is HS256 — i.e. whether the guarding backend is likely exploitable or crackable by the other two modes.

*No corpus evidence — derived from upstream documentation.*
