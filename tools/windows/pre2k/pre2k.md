---
name: "pre2k"
categories:
  - blackarch-windows
version: "24.ff51b3b"
homepage: "https://github.com/garrettfoster13/pre2k-TS"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "garrettfoster13/pre2k-TS"
github-stars: 10
github-pushed: "2025-07-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pre2k

> Finds and authenticates as pre-Windows-2000 machine accounts whose password is still the machine's name.

## What it's for

pre2k (pre2k-TS, "The Sequel", by Garrett Foster) enumerates a domain for computer objects configured as pre-Windows-2000 machines and tests whether their machine-account password is still the default — the machine's NetBIOS name lowercased and truncated to 14 characters. Such accounts often never rotate that password and can be authenticated against from any host, so a single valid pre-2K credential is a quick foothold or persistence hold in an assessed domain. It offers three approaches: a targeted, filtered search for computer accounts with logoncount 0; a full brute-force of all machine accounts; and unauthenticated validation of a user-supplied list of machine names. Valid credentials can be saved directly as Kerberos .ccache files, and spraying can be throttled with -sleep/-jitter and parallelized with -threads.

## Typical use cases

### Targeted search for stale pre-2000 machine accounts

With any valid domain credential, find and try likely pre-2K candidates.

```
python3 pre2k.py auth -u user -p 'Password1' -d example.com -dc-ip 198.51.100.10 -targeted
```

Outcome: binds to the domain controller over LDAP, collects computer accounts with logoncount 0, attempts authentication with each machine account's default password and reports every success; add -save to also request and store a usable .ccache ticket for each valid account.

### Validate a machine-name list without credentials

Check a pre-built list of machine account names (format `machinename$`) unauthenticated.

```
python3 pre2k.py unauth -d example.com -dc-ip 198.51.100.10 -inputfile machines.txt -outputfile valid.txt -stoponsuccess
```

Outcome: attempts Kerberos authentication for every name in machines.txt using the default password, logs hits to valid.txt and stops at the first success because of -stoponsuccess.

### Run the search with a hash instead of a password

Drive the authenticated modes via pass-the-hash.

```
python3 pre2k.py auth -u user -hashes aad3b435b51404eeaad3b435b51404ee:<NTHASH> -d example.com -dc-ip 198.51.100.10 -targeted
```

Outcome: same targeted search as the password variant, but binding with the supplied LMHASH:NTHASH pair instead of a plaintext secret.

*No corpus evidence — derived from upstream documentation.*
