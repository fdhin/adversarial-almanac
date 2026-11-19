---
name: "bruteforce-wallet"
categories:
  - blackarch-cracker
version: "43.648d7d7"
homepage: "https://github.com/glv2/bruteforce-wallet"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-03-02"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "glv2/bruteforce-wallet"
github-stars: 435
github-pushed: "2024-03-01"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bruteforce-wallet

> Recover the password of an encrypted Bitcoin, Litecoin or Peercoin `wallet.dat` by dictionary or exhaustive search.

## What it's for

bruteforce-wallet tries to find the encryption password of a cryptocurrency wallet file by attempting to decrypt one of the encrypted addresses the wallet contains — the same check the wallet client performs at unlock, so a password that validates here opens the wallet. It offers dictionary mode (one password per line) and exhaustive mode (generated from a character set between length bounds, with optional known beginning and end), multithreading, periodic progress reporting and a resumable state file. Two limits: the local BerkeleyDB library must be compatible with the version the wallet was created with (mismatched versions simply fail to read the file), and an unknown strong password is as out of reach here as in any exhaustive search — the tool is built for partially remembered wallet passwords.

## Typical use cases

### Attack a wallet with a wordlist

Try every line of a password file against the wallet's encrypted keys.

```
bruteforce-wallet -t 6 -f passwords.txt -v 30 wallet.dat
```

Outcome: six threads test the dictionary entries with a progress line every 30 seconds; on success stdout shows `Password found: <password>`, and an exhausted list prints `Password not found` on stderr.

### Exhaustive search around a partially remembered password

Generate candidates bounded by known fragments.

```
bruteforce-wallet -t 8 -l 5 -m 10 -b "W4l" -e "z" -s "0123456789abcdefghijklmnopqrstuvwxyz" wallet.dat
```

Outcome: eight threads try every 5-to-10-character string that starts with `W4l`, ends with `z` and draws only from the given character set, reporting the password when an address decrypts.

### Resume an interrupted search

Save and later continue the position of a long run.

```
bruteforce-wallet -t 6 -f passwords.txt -w state.txt wallet.dat
bruteforce-wallet -t 6 -w state.txt wallet.dat
```

Outcome: the first invocation records its position in state.txt roughly every minute; after stopping it, the second resumes the dictionary where it left off rather than restarting.

*No corpus evidence — derived from upstream documentation.*
