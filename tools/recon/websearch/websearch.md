---
name: "websearch"
categories:
  - blackarch-recon
version: "4.cb7ef8e"
homepage: "https://github.com/PentesterES/WebSearch"
packaged-by: [blackarch]
blackarch-builddate: "2023-02-20"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "PentesterES/WebSearch"
github-stars: 6
github-pushed: "2023-02-10"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# websearch

> Bing-powered vhost discovery: asks the search engine which hostnames resolve into a given IP range (passive reverse-DNS via search results).

## What it's for

WebSearch (PentesterES, José Selvi) answers "what hostnames live on this IP range?" without sending a single packet to the range: it queries Bing's API for `ip:`-style searches over the range and collects every hostname the index associates with those addresses — classic search-engine-based virtual host enumeration for the reconnaissance phase against hosting providers and CDNs. It takes a range and a Bing API key and prints the discovered name/address pairs. Treat it as a historical technique reference: the code is Python 2, the repository is archived with an explicit DEPRECATED notice, and the Microsoft/Cognitive Bing endpoint it targets is retired — the pattern itself (search-engine passive vhost discovery) is still sound and lives on in other tooling.

## Typical use cases

### Discover vhost names for an address range

Enumerate hostnames associated with a netblock before touching it.

```
python2 WebSearch.py -H 198.51.100.0-198.51.100.255 -K <bing_api_key>
```

Outcome: sweeps the range through Bing search queries and prints each hostname the engine associates with an address in the range (the key can also be hardcoded in the script to avoid passing it on the command line). Feed the discovered names to vhost-testing tooling (`vbrute`) to confirm which still respond; expect the archived tool's API calls to need re-pointing at a live search backend.

*No corpus evidence — derived from upstream source (option parser in WebSearch.py).*
