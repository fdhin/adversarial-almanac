---
name: "adassault"
categories:
  - blackarch-networking
version: "0.0.4.r28.g8c5aaba"
homepage: "https://noraj.github.io/ADAssault/"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/ADAssault"
github-stars: 19
github-pushed: "2026-11-02"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 30
gh-activity-checked: "2026-11-09"
---

# adassault

> Active Directory attack toolkit focused on DNS — fast domain-controller discovery and abuse of unsecure DNS zone dynamic updates.

## What it's for

ADAssault (by Alexandre ZANNI, aka noraj) is a Ruby command-line tool and library for pentesting Active Directory environments, positioned as a complement to NetExec-style suites rather than a replacement. Its current attack surface is DNS: `find_dcs` enumerates every domain controller (computer name, FQDN, IP addresses), and `duzdu` (DNS unsecure zone dynamic update) can add, check, delete, get and replace records in zones that accept unauthenticated dynamic updates. It installs both a full binary `adassault` and the short alias `ada`. It is not a general AD exploitation or credential suite — for that it expects you to keep using NetExec and friends.

## Typical use cases

### Enumerate the domain controllers

Query a domain's DNS server directly for all DC records.

```
ada dns -d example.com -s 198.51.100.10 find_dcs
```

Outcome: one line per domain controller in the form `NAME (fqdn) - IP` (long options `--domain`/`--nameserver` also work), giving the DC inventory to target next.

### Check a zone for unsecure dynamic updates

List the DUZDU subcommands and their options before touching any records.

```
ada help dns duzdu
```

Outcome: prints the `duzdu` subcommands (`add`, `check`, `delete`, `get`, `replace`) with their exact syntax, so `check`/`get` can be run first and records only modified on zones that accept unauthenticated dynamic updates.

### Use it as a Ruby library

The same features are callable from Ruby for scripting larger engagements.

```
ruby -e 'require "adassault";
  dcd = ADAssault::DNS::FindDCs.new("example.com", nameserver: ["198.51.100.10"]);
  puts dcd.dc_fqdn'
```

Outcome: prints the FQDNs of the domain's controllers; `dc_ip`, `dc_name` and the combined `dcs` hash are also available on the returned object.

*No corpus evidence — derived from upstream documentation.*
