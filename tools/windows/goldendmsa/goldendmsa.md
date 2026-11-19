---
name: "goldendmsa"
categories:
  - windows
homepage: "https://github.com/Semperis/GoldenDMSA"
origin: "hacktricks"
recipe: "upstream"
build-system: "windows"
meta-checked: "2026-11-13"
github-repo: "Semperis/GoldenDMSA"
github-stars: 100
github-match: "exact"
gh-checked: "2026-11-13"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "BSD-3-Clause"
---

# goldendmsa

> Offline password forgery for delegated Managed Service Accounts (gMSA/dMSA) from a stolen KDS root key.

## What it's for

GoldendMSA (Semperis) implements the "Golden dMSA" attack: once you
hold the Active Directory KDS root key, you can compute valid password
material for any gMSA or dMSA associated with that key — offline,
without touching a domain controller again — enabling authentication as
those service accounts and bypassing dMSA's takeover protections. The
workflow it automates: extract the KDS root key from a DC, enumerate
dMSA accounts (LDAP or RID brute-forcing), recover each account's
managedPasswordId (targeted wordlist + bruteforce), then generate and
convert passwords. It is the dMSA-era sibling of Semperis' GoldenGMSA.
C#/.NET tool: compile for x64 against .NET Framework 4.7.2, and ship
`CommandLine.dll` next to the executable on the target.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Extract the KDS root key

Phase 1 — get the key material everything else depends on.

```
GoldendMSA.exe kds
GoldendMSA.exe kds --domain <domain name>
```

Outcome: lists the forest's KDS root keys. The first form needs
Enterprise Admins rights; the `--domain` form works with SYSTEM on a
domain controller. `kds -g <guid>` narrows the dump to one key.

### Enumerate dMSA/gMSA accounts

Phase 2 — discover targets and their SIDs.

```
GoldendMSA.exe info -d <domain name> -m ldap
GoldendMSA.exe info -d <domain name> -m brute -u <username> -p <password> -o <user's domain name> -r <number>
```

Outcome: LDAP mode returns the managed service accounts known to the
domain; brute mode authenticates with low-priv credentials and
enumerates by RID range (`-r`) or from a known gMSA SID (`-s
<gMSA's sid>`), yielding SamAccountName + SID pairs to attack.

### Build a managedPasswordId wordlist

Phase 3 — the candidate space for the password ID guessing.

```
GoldendMSA.exe wordlist -s <dMSA's sid> -d <dMSA's domain> -f <forest's domain> -k <id of kds root key>
```

Outcome: writes a wordlist of plausible managedPasswordId values for
the target dMSA, derived from its SID, domain and the KDS root key ID.

### Bruteforce the dMSA password ID

Pin down the correct managedPasswordId and hash.

```
GoldendMSA.exe bruteforce -s <sid of dmsa> -i <kds root key id> -k <kds root key> -d <dmsa's domain> -u <dmsa (should end with $)> -t
```

Outcome: tests the wordlist against the target dMSA and reports the
matching managedPasswordId with its password hash; `-v` runs the
verbose variant that prints each attempt.

### Compute a gMSA/dMSA password

Phase 4 — forge the password from key + password ID.

```
GoldendMSA.exe compute -s <sid> -k <KDS Root key> -d <domain name> -m <ManagedPasswordID>
```

Outcome: computes the account's current password offline from the KDS
root key and managedPasswordId — no further contact with the DC.

### Convert a base64 password to kerberos keys

Turn a dumped/computed password into usable key material.

```
GoldendMSA.exe convert -d <domain name> -u <username end with $> -p <base64 password>
```

Outcome: prints the NTLM, AES128 and AES256 keys for the service
account, ready for overpass-the-hash style tooling.
