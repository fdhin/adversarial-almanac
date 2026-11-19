---
name: "netmask"
categories:
  - blackarch-recon
version: "2.4.4"
homepage: "http://packages.qa.debian.org/n/netmask.html"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2019-02-10"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "tlby/netmask"
github-stars: 40
github-pushed: "2025-08-06"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netmask

> Netmask generation and conversion utility: turns any address specification into the smallest list of subnets covering it, in the notation your firewall or router wants.

## What it's for

netmask (by Robert Stone, with algorithm work by Tom Lear) is a small console calculator for IPv4 address and netmask arithmetic. You hand it one or more *specs* — a single address, an `address1:address2` range, `address1:+count`, or `address/mask` in dotted-quad, Cisco inverse-mask, or CIDR-bit form — and it converts them into a minimal, optimized list of subnets, printed in a choice of formats (dotted netmask pairs, CIDR, Cisco inverse mask, ranges, hex, octal, or binary). It is the tool for cleaning up sloppy firewall rule lists and translating between vendor notations; it does no scanning and sends no packets (hostnames in specs are resolved via DNS unless `-n` is given). Pure local math, useful wherever ranges and masks must be rewritten.

## Typical use cases

### Show a subnet as an address/netmask pair

Convert a CIDR spec into classic dotted-quad mask form.

```
netmask -s 198.51.100.0/24
```

Outcome: prints `  198.51.100.0/255.255.255.0` — the spec as one or more address/netmask pairs, which is the format most firewall rule files expect.

### Find the minimal CIDR block for an arbitrary range

Collapse a start:end range into the smallest CIDR list that covers exactly it.

```
netmask -c 198.51.100.16:198.51.100.31
```

Outcome: prints `  198.51.100.16/28` — the single optimal /28 covering the 16-address range; non-aligned ranges yield several CIDR lines, giving you the tightest rule set instead of a spanning superblock.

### Emit a Cisco inverse (wildcard) mask

Produce the `address wildcard` form used in Cisco ACLs.

```
netmask -i 198.51.100.0/24
```

Outcome: prints `  198.51.100.0 0.0.0.255` — the prefix with its inverse mask, ready to paste into an access-list entry.

### Expand a subnet into explicit address ranges

Show first-last ranges with the address count per line.

```
netmask -r 198.51.100.64:198.51.100.95
```

Outcome: prints `  198.51.100.64-198.51.100.95   (32)` — start, end, and size of each covering block, useful for documentation and allocation planning.

### Hex or octal pair output

For tools that consume non-decimal mask notations.

```
netmask -x 198.51.100.0/30
```

Outcome: prints `0xc6336400/0xfffffffc` — the address/netmask pair in hexadecimal (`-o` gives octal, `-b` binary).

*No corpus evidence — derived from upstream documentation (Debian netmask 2.4.4 manpage; output shapes verified by compiling and running the 2.4.4 source).*
