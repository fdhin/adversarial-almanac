---
name: "zdns"
categories:
  - blackarch-networking
version: "v2.1.1.r8.g81eff11"
homepage: "https://github.com/zmap/zdns"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-08-02"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "zmap/zdns"
github-stars: 1152
github-pushed: "2026-10-18"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 49
gh-activity-checked: "2026-11-09"
---

# zdns

> Fast bulk DNS lookup tool from the ZMap project: names on stdin, one JSON result per line, built for internet-scale resolution.

## What it's for

zdns is a command-line DNS resolver designed for high-throughput bulk lookups — millions of names — as used by the ZMap ecosystem's measurement studies. It takes names (or name/server pairs) on stdin or from a file, queries in parallel, and emits one JSON record per input line containing the answer, resolvers used, and timing. Besides single record-type modules (A, MX, NS, and friends) it has chain-following lookup modules (e.g. `mxlookup` resolves a domain's mail hosts through to addresses), full iterative resolution from the root servers (`--iterative`), per-line nameserver auditing (`--name-server-mode`), and a MULTIPLE mode that runs several query types per domain from an INI file. It resolves the names you feed it — it is a fast resolver front-end, not a zone walker.

## Typical use cases

### Resolve a list of domains in bulk

Turn a name list into structured answers.

```
cat domains.txt | zdns A
```

Outcome: Resolves every name on stdin in parallel and prints one JSON line per name with the A records (or the error/status) — millions of names are practical; a single name works the same way: `echo example.com | zdns A`.

### Resolve many names against a chosen nameserver

Point every query at one specific resolver, e.g. one under test.

```
zdns A --input-file=domains.txt --name-servers=198.51.100.10
```

Outcome: Reads domains from the file and resolves them all using only 198.51.100.10 as the nameserver instead of the system default, letting you audit what that resolver answers at scale. Per-line servers also work: feed `example.com,198.51.100.10` pairs on stdin.

### Perform iterative resolution

Recurse from the root servers down, bypassing the local resolver.

```
echo example.com | zdns A --iterative
```

Outcome: zdns walks the delegation chain itself — root, TLD, authoritative — and reports the answer it assembled, showing what any recursive resolver would find, independent of local caching.

### Audit many resolvers for one name

Flip the input: a list of nameservers, one fixed query.

```
echo "198.51.100.10" | zdns A --name-server-mode --override-name="example.com"
```

Outcome: In name-server mode each stdin line is treated as a resolver to query, with `--override-name` fixing the queried name — so a file of resolver IPs yields a per-resolver answer matrix for example.com.

### Run multiple query types per domain

Mix record types in one pass.

```
zdns MULTIPLE --multi-config-file=./multiple.ini --input-file=input.csv
```

Outcome: For each input domain, runs the query types configured in the INI file (A, MX, NS, and so on) and emits their combined results, avoiding repeated passes over large inputs.

### Follow a mail domain to deliverable hosts

Resolve MX records through to their addresses.

```
echo "example.com" | zdns mxlookup --ipv4-lookup
```

Outcome: Returns the domain's MX hosts resolved to IPv4 addresses — the full mail-delivery chain in one record instead of separate MX and A queries.

*No corpus evidence — derived from upstream documentation.*
