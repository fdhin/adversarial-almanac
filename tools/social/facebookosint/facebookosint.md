---
name: "facebookosint"
categories:
  - blackarch-social
version: "22.3fb03f5"
homepage: "https://github.com/tomoneill19/facebookOSINT"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-06"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "tomoneill19/facebookOSINT"
github-stars: 146
github-pushed: "2026-08-26"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# facebookosint

> Interactive console that reconstructs Facebook graph-search-style queries to surface the public posts and profile information a target page exposes.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Written as a replacement for the Facebook graph search that was removed on 19/06/19: you set a target page, a search-box query, and optional filters, and the tool composes the corresponding search URL — opened in your browser or printed for scripting. The author marks it effectively deprecated because the underlying API is discontinued and would need reimplementing, so treat it as a reference for how graph-search queries were rebuilt rather than dependable tooling. All interaction happens through an interactive console; BlackArch packages it as the `facebookosint` command wrapping the Python source.

## Typical use cases

### Open the query console

Start the tool and list what it can do.

```
facebookosint
```

Outcome: drops into the interactive prompt — `help` lists every command and `listvars` shows all variables of the current query.

### Compose and open a graph-search query

Turn target plus query plus filters into a working search URL.

```
settarget https://www.facebook.com/target
setquery photos
addfilter
getposts
```

Outcome: composes the query against the target page (identified by name, ID or URL via `settarget`) and opens the result in your browser; run `getposts url` instead to print the URL to the terminal for reuse elsewhere.
