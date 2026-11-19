---
name: "unfurl"
categories:
  - blackarch-misc
version: "16.99ad735"
homepage: "https://github.com/tomnomnom/unfurl"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-04-27"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "tomnomnom/unfurl"
github-stars: 1341
github-pushed: "2023-08-12"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# unfurl

> Pulls out bits of URLs provided on stdin.

*No corpus evidence — derived from upstream documentation.*

## What it's for

unfurl (Tom Hudson / tomnomnom, Go) is a stream filter for URL lists: URLs
go in on stdin, a mode picks what comes out — one extracted element per
line. Modes cover hostnames, apex domains, paths, query-string keys,
values, and key=value pairs, plus a full JSON parse and a printf-style
`format` mode for reassembling URLs in a custom shape. It is the standard
companion for chewing through large recon URL dumps (web archives, crawler
output) when building target lists or harvesting parameter names. It parses
only — it never fetches anything.

## Typical use cases

### Extract unique hostnames

Build a scope list from a URL dump.

```
cat urls.txt | unfurl -u domains
```

Outcome: prints each URL's hostname one per line (`sub.example.com`), with
duplicates removed by `-u`/`--unique` — ready to feed to DNS resolution or
an HTTP prober.

### Reduce subdomains to apex domains

Collapse a noisy subdomain list to registrable roots.

```
cat urls.txt | unfurl -u apexes
```

Outcome: prints the apex domain per URL (e.g. `example.com` from
`https://sub.example.com/...`), deduplicated.

### Harvest query parameter names

Inventory the parameters a site actually uses.

```
cat urls.txt | unfurl -u keys
```

Outcome: one query-string key per line (`id`, `name`, ...), deduplicated —
a quick parameter-mining list; the `keypairs` mode prints `key=value`
pairs and `values` bare values.

### Reshape URLs with a format string

Rebuild URLs in exactly the form you want.

```
cat urls.txt | unfurl format %s://%d%p
```

Outcome: reassembles each URL from its parts — here scheme, domain and path
(`https://sub.example.com/users`); directives also cover subdomain, root,
TLD, port, query string, fragment, file extension and more.

### Parse URLs to JSON

Keep every field for downstream tooling.

```
cat urls.txt | unfurl json
```

Outcome: one JSON object per input URL containing scheme, host, path,
query parameters and the domain breakdown (subdomain/root/TLD/apex) —
process further with `jq`.
