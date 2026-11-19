---
name: "waymore"
categories:
  - blackarch-recon
version: "v8.9.r0.g4e02de5"
homepage: "https://github.com/xnl-h4ck3r/waymore"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-06-14"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "xnl-h4ck3r/waymore"
github-stars: 2790
github-pushed: "2026-06-11"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 30
gh-activity-checked: "2026-11-09"
---

# waymore

> Gets "way more" from web archives: URLs and full archived response bodies from Wayback Machine, Common Crawl, AlienVault OTX, URLScan, VirusTotal and Intelligence X.

## What it's for

waymore (xnl-h4ck3r) is the heavyweight successor to single-source archive tools: one target domain in, and it fans out to six sources for every known URL — then optionally downloads the archived response bodies themselves (Wayback, URLScan), not just the links. Those stored responses are the point for many workflows: old JavaScript with endpoints and keys, deleted JSON, previous versions of pages. Runs are controlled by `-mode U` (URLs only), `-mode R` (responses only) or `-mode B` (both, the default for a bare domain), with rich filtering (`-fc/-mc` status codes, `-mt` MIME types, `-ra` regex over links, `-ko` keyword pre-filter), date windows (`-from`/`-to`), per-source exclusions (`-xwm`, `-xcc`, ...), rate-limit handling and memory guards. Results land under a per-domain directory in `~/.config/waymore/` unless redirected. VirusTotal and Intelligence X need API keys in `config.yml`; everything else works keyless within rate limits.

Commands below are verified against the upstream README.

## Typical use cases

### Get every known URL for a domain

Link discovery across all sources.

```
waymore -i example.com -mode U
```

Outcome: queries Wayback, Common Crawl, AlienVault OTX, URLScan, VirusTotal and Intelligence X for `example.com` and all subdomains, writing the deduplicated URL list to `waymore.txt` under the results directory; add `--stream` to also emit URLs to stdout as they are found for immediate piping.

### Download archived response bodies

Mine how the site used to look, including old JS.

```
waymore -i example.com -mode B -oU urls.txt -oR responses/
```

Outcome: as above, plus downloads up to 5,000 archived responses (raise/lower with `-l`, `-l 0` for all) into `responses/`, one file per unique response — a corpus you can grep for endpoints, secrets and forgotten functionality.

### Only JavaScript, please

Filter the harvest down to the files that usually pay.

```
waymore -i example.com -mode U -ko '\.js(\?.*\|$)'
```

Outcome: the keyword filter keeps only links matching the JS pattern (applied as a pre-filter at the CDX API and exactly locally), cutting run time and output size; `-ra '\.js$'` is the equivalent post-filter over both links and downloaded responses.

### Size up a big target first

Estimate cost before committing to a full pull.

```
waymore -i example.com --check-only
```

Outcome: makes a few minimal requests and reports how many requests a full run would need and roughly how long it would take, so you can decide on `-l`, `-from`/`-to` or per-source exclusions before starting.
