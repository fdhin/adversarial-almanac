---
name: "targetedkerberoast"
categories:
  - blackarch-windows
version: "27.ebed079"
homepage: "https://github.com/ShutdownRepo/targetedkerberoast"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: windows
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "ShutdownRepo/targetedkerberoast"
github-stars: 694
github-pushed: "2024-12-16"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# targetedkerberoast

> Kerberoasting tool that first abuses SPN-write rights on accounts that have no SPN at all.

## What it's for

targetedKerberoast is a Python script (ShutdownRepo) that prints crackable Kerberos service-ticket hashes for user accounts with an SPN — and, unlike `GetUserSPNs.py`, also for accounts without one: when it holds a write privilege on a user's `servicePrincipalName` attribute, it temporarily sets an SPN, requests the ticket, prints the hash, then deletes the SPN ("targeted Kerberoasting"). It works against a whole domain, a user list, or one named user, and authenticates with a password, NT hash (pass-the-hash), Kerberos keys or a cached TGT. Reach for it during AD review when object-level ACLs (GenericWrite, GenericAll, WriteProperty on `servicePrincipalName`) let you turn a non-SPN account into a roastable one. It does not crack the hashes — feed its output to hashcat or JtR.

## Typical use cases

### Roast every account in the domain with a password

One authenticated pass that collects hashes both from existing SPNs and from accounts whose SPN you can temporarily write.

```
targetedkerberoast.py -v -d example.com -u user --dc-ip 198.51.100.10
```

Outcome: prompts for the password, then prints one `user:domain:...` kerberoast hash per roastable account in hashcat format; watch the per-user lines showing the temporary SPN being set and removed.

### Target a single user

Request a ticket for just the SPN associated with one named account — useful when you know exactly which principal you can abuse.

```
targetedkerberoast.py -d example.com -u user --request-user victim --dc-ip 198.51.100.10
```

Outcome: prints only that account's hash (or an error if it has no SPN and is not abusable).

### Pass-the-hash instead of a password

Authenticate with a captured NT hash, the standard flow after an NTLM credential dump.

```
targetedkerberoast.py -d example.com -u user -H :NTHASH --dc-ip 198.51.100.10
```

Outcome: same enumeration and hash output as with a password, no cleartext credential needed (`-H` accepts `lmhash:nthash`).

### Abuse-only run with offline-cracking output

Skip accounts that already have SPNs and write everything to a file in John the Ripper format.

```
targetedkerberoast.py -d example.com -u user --only-abuse -o hashes.txt -f john --dc-ip 198.51.100.10
```

Outcome: writes only the hashes obtained via temporary-SPN abuse to `hashes.txt` with usernames prefixed for JtR; crack with `john hashes.txt`.

*Corpus evidence: `targetedkerberoast.py --dc-ip <ip> -d <domain> -u <user>` invocations across two reports; flags verified against the ShutdownRepo README usage block.*
