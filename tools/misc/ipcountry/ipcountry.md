---
name: "ipcountry"
categories:
  - blackarch-misc
version: "2.3"
homepage: "https://nullsecurity.net/tools/misc.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-05"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# ipcountry

> Fetch a country's IP ranges (host, CIDR, or JSONL) from RIR delegation data, and reverse-lookup which country owns an IP.

## What it's for

ipcountry (nullsecurity, by noptrix) is a small Python CLI over the Regional Internet Registry delegation zones: give it one or more country codes and it downloads the current zone data and writes that country's IPv4 (or IPv6) ranges in host, CIDR, or JSONL form — per-country files by default, or one stream to stdout for piping. It also works in reverse: feed it IPs and it reports the owning country code. Reach for it when building scoped target lists, allow/deny lists, or doing GeoIP lookups without maintaining a local database.

## Typical use cases

### Collect ranges for specific countries

```
ipcountry -c am,gr,cy
```

Outcome: writes one file per country code (host and CIDR range types by default) into the current directory, built from freshly downloaded RIR delegation data.

### Stream CIDRs into another tool

```
ipcountry -c ru -t cidr -o -
```

Outcome: prints raw CIDR ranges to stdout so they can be piped straight into a scanner or filtered with `grep`/`wc` — no intermediate files. `-t host,cidr` mixes formats and `-i` switches to IPv6 ranges.

### Reverse-lookup IPs to countries

```
ipcountry -x 198.51.100.10
```

Outcome: prints the country code that owns the IP; `-x -` reads one IP per line from stdin, so extracted logs can be annotated in bulk.

### Build a full dataset

```
ipcountry -c all -j -o ranges.jsonl
```

Outcome: dumps every country's ranges as JSONL (one `{country, type, range}` object per line) into a single ready-to-import file; `-l` instead just lists all country codes with their full names.

*No corpus evidence — derived from the packaged source. The tool lives in the nullsecuritynet tools collection under `misc/ipcountry`; beware same-named GitHub repos that are unrelated IP-country database projects.*
