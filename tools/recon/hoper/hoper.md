---
name: "hoper"
categories:
  - blackarch-recon
version: "v0.1.1.r0.g8d5dbd9"
homepage: "https://github.com/gabamnml/hoper"
packaged-by: [blackarch]
blackarch-builddate: "2025-10-02"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "gabamnml/hoper"
github-stars: 56
github-pushed: "2026-11-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# hoper

> Trace every redirect hop a URL takes and show where it really lands.

## What it's for

hoper follows a URL through its chain of HTTP redirects, printing the status
code, reason and response time of every hop until the final destination —
the tool for unwinding shorteners, tracking links, SSO bounce chains and
misbehaving redirect rules. It defaults to HEAD requests (falling back to
GET for servers that reject HEAD), detects redirect loops, caps the walk at
a configurable hop limit, and honours proxy environment variables. For
scripts it emits JSON (`-j`) with start/final URLs and the full hop list,
and its exit codes carry meaning: 0 when every URL resolved below 400, 1
when any hop failed. Scheme-less arguments get `https://` assumed.

## Typical use cases

### Follow a shortener

```
hoper https://example.com/shortlink
```

Outcome: a numbered hop list with per-hop status and timing, ending with the
final URL and the total redirect count — where the link actually takes you.

### Machine-readable traces of several URLs

```
hoper --json -m 10 -t 5 https://example.com/a https://example.com/b
```

Outcome: one JSON object per URL (an array when several are given) with
`start_url`, `final_url`, `final_status` and every hop's
url/status/location/duration — consumable by `jq`; exit code 1 flags any
link that died mid-chain.

*No corpus evidence — derived from upstream documentation.*
