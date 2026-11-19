---
name: "smtp-vrfy"
categories:
  - blackarch-scanner
version: "1.0"
homepage: ""
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# smtp-vrfy

> Perl script that walks a name list through the SMTP VRFY command and prints every account the server confirms.

## What it's for

This is `vrfy.pl` by B-r00t (2001), packaged by BlackArch as `smtp-vrfy`. It connects to port 25 on a target, sends `HELO`, then issues `VRFY <name>` for every entry in a name list and prints only the positive (25x code) replies — a minimal SMTP account enumerator. A small list of common system account names ships with the package at `/usr/share/smtp-vrfy/names.txt`. Servers that disable VRFY (common) return nothing useful; the tool has no RCPT-probing or EXPN fallbacks, so on hardened mail servers pair it with other enumeration approaches.

## Typical use cases

### Enumerate accounts with the bundled name list

```
smtp-vrfy -h mail.example.com -l /usr/share/smtp-vrfy/names.txt
```

Outcome: connects to 25/TCP, issues `VRFY` for each name in the shipped list and prints one response line per account the server confirms (25x codes) — the confirmed names are valid mailbox/system accounts for follow-on password testing.

### Walk a custom username list

```
smtp-vrfy -h mail.example.com -l users.txt
```

Outcome: same VRFY walk over your own list — e.g. name permutations generated from harvested employee names — reporting every name the server confirms as existing.

*No corpus evidence — derived from upstream documentation (packaged source from Packet Storm, authored by B-r00t).*
