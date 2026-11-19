---
name: "kerbrute"
categories:
  - blackarch-cracker
version: "90.9cfb81e"
homepage: "https://github.com/ropnop/kerbrute"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-04-27"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "ropnop/kerbrute"
github-stars: 3489
github-pushed: "2024-08-20"
github-archived: false
github-issues: 47
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kerbrute

> Fast Active Directory username enumeration and password testing over Kerberos pre-auth.

## What it's for

kerbrute (Go, by ropnop) validates usernames and passwords against a Windows domain by talking Kerberos directly to a Domain Controller. Username enumeration exploits pre-auth behavior: a TGT request for an existing user comes back asking for pre-authentication, while a non-existent user returns an error — and enumeration generates no failed logons, so it cannot lock accounts out. The password modes (spray, single-user brute force, combo testing) do count as failed logons and can lock accounts, so they are for when you accept that risk; pre-auth failures show up as Kerberos event 4771/4768 rather than the classic 4625. It needs reachability to a DC (KDC) on port 88 and either the domain name (resolved via DNS) or an explicit `--dc`. An older Python tool of the same name existed with a single-flag interface (`-users file -password x`); current releases use the subcommand interface documented here.

## Typical use cases

### Enumerate valid domain usernames

```
kerbrute userenum -d corp.example.com --dc dc01.corp.example.com users.txt
```

Outcome: sends pre-auth-free TGT requests for every candidate name; existing accounts are printed as `[+] VALID USERNAME: user@corp.example.com` with a final count and timing summary. Lockout-safe — this is the standard first step to turn a name list into confirmed accounts. Add `-o valid_users.txt` to keep results in a file.

### Password-spray a user list with one password

```
kerbrute passwordspray --dc 198.51.100.10 -d corp.example.com users.txt Password1
```

Outcome: tests the single password against every user via Kerberos pre-auth and prints `[+] VALID LOGIN: user@corp.example.com:password` for hits plus a tested/success summary. Use `--safe` to abort automatically if any account reports locked out, and `--delay 500` to rate-limit attempts (forces single-threaded).

### Brute-force one user's password from a wordlist

```
kerbrute bruteuser -d corp.example.com --dc 198.51.100.10 passwords.lst user
```

Outcome: walks the wordlist as the single user's password, reporting `[+] VALID LOGIN` on a hit. Warning: every miss increments that account's bad-password counter, so only run this where lockout policy is absent or acceptable.

### Test username:password combos from a file or pipe

```
cat combos.txt | kerbrute -d corp.example.com bruteforce -
```

Outcome: reads `username:password` lines (file argument or stdin as here, blank lines skipped) and validates each pair against the KDC, printing valid logins — useful for replaying previously harvested credential dumps.

### Capture AS-REP hashes while spraying

Some accounts have pre-auth disabled; asking them for a TGT returns a crackable AS-REP.

```
kerbrute userenum -d corp.example.com --dc dc01.corp.example.com users.txt --hash-file asreps.txt
```

Outcome: any user found without pre-authentication has their AS-REP hash written to `asreps.txt` (otherwise only logged), ready for offline cracking with a hashcat/John AS-REP mode; users with pre-auth enabled are still enumerated as usual.
