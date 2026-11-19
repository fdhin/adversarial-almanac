---
name: "fav-up"
categories:
  - blackarch-recon
version: "54.089aa11"
homepage: "https://github.com/pielco11/fav-up"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-04"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "pielco11/fav-up"
github-stars: 1208
github-pushed: "2025-02-05"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fav-up

> Finds the real IP behind a CDN (e.g. Cloudflare) by hashing a site's favicon and searching Shodan for hosts serving the same icon.

## What it's for

fav-up (pielco11) is an IP-lookup-by-favicon tool: it computes the favicon hash of a website and asks Shodan which other hosts on the internet serve the same favicon, exposing origin servers that still expose the site's icon behind a CDN. Reach for it during bypass-CDN recon in bug bounty or infrastructure mapping. It needs a Shodan API key that is not the free one; pass it with `-k`, from a file with `-kf`, or reuse an initialized Shodan CLI with `-sc`. Besides the CLI it is usable as a Python module (`from favUp import FavUp`), with results in `faviconsList` carrying the favicon hash, the masked (CDN) IP/ISP and the found real IPs. If no match is found it returns `not-found`.

## Typical use cases

### Lookup from a local favicon file

You already saved the site's icon.

```
python3 favUp.py --favicon-file favicon.ico -sc
```

Outcome: hashes the local favicon, queries Shodan for hosts serving the same icon and prints each match's real IP list — the origin candidates behind the CDN.

### Lookup from the favicon URL

You know where the icon lives but don't have it locally.

```
python3 favUp.py --favicon-url https://example.com/assets/favicon.ico -sc
```

Outcome: fetches the favicon, hashes it and returns the Shodan matches together with the domain's masked (CDN) IP and ISP for contrast.

### Lookup from just the site

You only know the site's domain.

```
python3 favUp.py --web example.com -sc
```

Outcome: discovers the favicon URL from the page's `<link rel='icon'>` element, then runs the hash-and-search flow, printing real IPs behind the domain.

### Hunt by known hash across the internet

You already have the mmh3 favicon hash.

```
python3 favUp.py --favicon-hash <favicon_hash> -k <SHODAN_API_KEY>
```

Outcome: skips fetching entirely and searches Shodan internet-wide for the hash, reporting every host matching the fingerprint. Bulk input (`-fl`, `-ul`, `-wl`) and CSV/JSON output (`-o results.csv`) work with all lookup modes.

No corpus evidence — derived from upstream documentation.
