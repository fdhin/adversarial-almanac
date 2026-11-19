---
name: "whois"
categories:
  - recon
version: "git (2026-07-25)"
homepage: "https://github.com/rfc1036/whois"
origin: "github"
recipe: "upstream"
packaged-by: [blackarch, arch, nixpkgs]
build-system: make
meta-checked: "2026-11-09"
github-repo: "rfc1036/whois"
github-stars: 634
github-pushed: "2026-07-25"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 17
gh-activity-checked: "2026-11-09"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
blackarch-builddate: "2024-12-26"
arch-builddate: "2026-02-17"
github-issues: 34
---

# whois

> Intelligent RFC 3912 whois client that picks the right registry server for a domain, IP or AS number and prints the record.

## What it's for

This is the classic GNU/Linux whois client (Marco d'Itri, rfc1036/whois): give it an object and it queries the RFC 3912 directory service, guessing the authoritative server — following referrals, walking TLD-specific registrars, and falling back to ARIN for IPv4 addresses. Use it for registration data (registrant, registrar, dates, nameservers), IP netblock ownership and abuse contacts, and for interrogating non-standard whois services on arbitrary hosts and ports. It is a lookup client only — it neither registers domains nor scrapes web pages, and modern registries increasingly return thin/redacted records (RDAP is the successor protocol; this client speaks classic whois, with RIPE-style `-i/-s/-T` query modifiers for database servers that support them).

Commands below are verified against the upstream man page (`whois.1`).

## Typical use cases

### Look up a domain registration

Pull the registrar record for a domain.

```
whois example.com
```

Outcome: resolves the TLD's whois server, prints the full record — registrar, registration/expiry dates, nameservers, registrant contact data (or a redaction notice), and any referral to a second-level server which the client follows automatically.

### Identify who owns an IP address

Find the operator and abuse contact for a netblock.

```
whois 198.51.100.10
```

Outcome: queries the regional internet registry (ARIN/RIPE/APNIC...) for the enclosing netblock and prints the organisation, address range, and abuse/NOC contacts. Chaining `whois -I 198.51.100.10` first asks `whois.iana.org` and follows its referral to the correct registry — useful for allocations that have been transferred.

### Query a custom whois server on a specific port

Interrogate a dedicated whois service rather than a public registry.

```
whois -h 198.51.100.20 -p 43 "example.com"
```

Outcome: opens a raw TCP connection to `198.51.100.20` port 43, sends `example.com` as the query string and echoes whatever the server replies — the standard way to probe a whois service running on a host you control or discovered during testing. Server selection can also be mapped persistently per-domain via a `whois.conf` file (`/etc/whois.conf` or `/usr/local/etc/whois.conf`), which the client reads before connecting.
