---
name: "cidr2range"
categories:
  - blackarch-networking
version: "1.0"
homepage: "http://www.cpan.org/authors/id/R/RA/RAYNERLUC"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/cidr2range"
github-stars: 0
github-pushed: "2019-09-09"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cidr2range

> Expand a CIDR netblock into an address range, an address count, or one IP per line.

*No corpus evidence — derived from upstream documentation.*

*GitHub mirror: `Seabreg/cidr2range` — a faithful mirror, byte-identical to the CPAN-packaged script by Rayner Lucas.*

## What it's for

A small Perl utility (by Rayner Lucas, distributed via CPAN) that converts CIDR notation — a single `address/mask` argument — into the first-to-last address range, the number of addresses, or the full list of addresses in the block. It is the kind of helper you reach for when scoping a target range, building firewall or allow-list rules, or generating input files for other tools. It handles exactly one netblock per invocation and validates the address and mask (0–32) before doing anything; masks beyond /32 or malformed octets exit with an error. With no options it defaults to printing the range.

## Typical use cases

### Show the range of a netblock

Get the first and last address of a block.

```
cidr2range 198.51.100.0/24
```

Outcome: prints `Address range: 198.51.100.0-198.51.100.255` — the default behavior when no option is given.

### Count the addresses in a block

Check block size before generating anything.

```
cidr2range -n 198.51.100.0/23
```

Outcome: prints `512 addresses.` — the count is 2^(32 minus mask bits), so a /23 yields 512.

### List every address, one per line

Produce an IP list to feed into another tool.

```
cidr2range -l 198.51.100.0/30
```

Outcome: prints each address from 198.51.100.0 to 198.51.100.3, one per line — pipe it straight into a scanner or loop.

### Get count, range and full list together

One invocation, all three views.

```
cidr2range -n -r -l 198.51.100.0/29
```

Outcome: prints the address count, the `Address range:` line, and then all addresses of the block — the three options combine freely.
