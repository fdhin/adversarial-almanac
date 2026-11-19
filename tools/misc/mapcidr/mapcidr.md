---
name: "mapcidr"
categories:
  - blackarch-misc
version: "v1.1.97.r247.g7d653d1"
homepage: "https://github.com/projectdiscovery/mapcidr"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/mapcidr"
github-stars: 1241
github-pushed: "2026-11-02"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 244
gh-activity-checked: "2026-11-09"
---

# mapcidr

> Swiss-army CLI for CIDR ranges: expand, slice, aggregate, filter and convert IPv4/IPv6.

## What it's for

mapCIDR (projectdiscovery) performs bulk operations on subnets and IP lists from the command line or stdin. It enumerates every address in a CIDR or an explicit IP range, splits ranges into a given number of subnets or into subnets of a fixed host count, aggregates scattered IPs back into minimal CIDR blocks (exactly or approximately), matches or filters IPs against ranges, counts hosts, expands an ASN into its announced addresses, and rewrites addresses between notations (IPv4↔IPv6, integer, hex, octal, binary, URL-encoded). It is the standard glue for building a scoped target list before a scan and for deduplicating or summarizing address data afterwards. IPv4 and IPv6 are both handled, input comes from files or stdin, and output is plain lines — so it drops cleanly into pipes.

## Typical use cases

### Expand a CIDR to an address list

Enumerate every host in a range.

```
mapcidr -cidr 198.51.100.0/24 -silent
```

Outcome: one IP per line for the whole /24, ready to pipe into a scanner; drop `-silent` to also print the banner and warnings. An explicit range works too: `echo 198.51.100.0-198.51.100.5 | mapcidr`.

### Split a range into equal subnets

Slice a block for distribution across teams or scans.

```
mapcidr -cidr 198.51.100.0/24 -sbc 4 -silent
```

Outcome: four /26 subnets covering the input block (`-sbc` = slice by CIDR count). `-sbh 64` instead slices by host count — subnets of 64 hosts each; power-of-two targets split exactly, others are approximated.

### Aggregate IPs into minimal CIDR blocks

Summarize a scattered host list.

```
mapcidr -il ips.txt -aggregate
```

Outcome: the smallest set of CIDR blocks covering exactly the IPs in `ips.txt`; add `-aggregate-approx` to tolerate gaps and reach fewer, larger blocks. The reverse also works — `mapcidr -cl cidrs.txt -aggregate` merges a list of ranges into covering blocks.

### Keep or drop specific addresses

Filter scope before scanning.

```
mapcidr -cidr 198.51.100.0/24 -fi exclude.txt -silent
```

Outcome: all addresses of the /24 except those listed in `exclude.txt`; `-mi` inverts the behavior and prints only addresses that match the given list or comma-separated IPs.

### Expand an ASN into addresses

Turn routing data into a target list.

```
echo AS15133 | mapcidr -silent
```

Outcome: the IP addresses announced by that ASN, one per line — a quick way to scope a scan to an organization's announced space.

### Convert notations and count a scope

Rewrite addresses and size a range.

```
echo 198.51.100.0/24 | mapcidr -count -silent
mapcidr -cl ips.txt -t6
```

Outcome: the first command prints the address count (256); the second rewrites each IPv4 in `ips.txt` into IPv6-mapped form. `echo 198.51.100.10 | mapcidr -if 0 -silent` prints a single IP in all ten supported notations (hex, integer, binary, URL-encoded, ...).

*No corpus evidence — derived from upstream documentation.*
