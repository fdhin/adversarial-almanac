---
name: "cangibrina"
categories:
  - blackarch-scanner
version: "123.6de0165"
homepage: "https://github.com/fnk0c/cangibrina"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "fnk0c/cangibrina"
github-stars: 239
github-pushed: "2018-11-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cangibrina

> Admin dashboard finder that combines wordlists, search-engine dorks, robots.txt, TOR routing, and nmap service data.

## What it's for

Cangibrina (by fnk0c) locates the admin panel or dashboard of a website using several strategies in one tool: fast wordlist brute forcing over paths (a bundled medium list by default), search-engine queries via `--search` with optional custom dorks (`--dork 'inurl:login.php'`), candidates derived from robots.txt, and `--nmap`, which matches likely dashboard locations against the target's detected services and ports. It can route through TOR, rotate the user-agent, filter candidates by extension (`--ext php`), and switch from directories to subdomain hunting with `--sub-domain`. It is Python 2 based and dormant since 2018, but the finder logic itself still runs.

## Typical use cases

### Fast default search

```
cangibrina -u example.com -v
```

Outcome: brute-forces the bundled wordlist with 5 threads and prints each admin-path candidate found, with verbose progress.

### Custom wordlist and thread count

```
cangibrina -u example.com -w paths.txt -t 10 -v
```

Outcome: hunts over your own path list with 10 threads; `--ext php` keeps only candidates matching the target's extension.

### Dork-driven search

```
cangibrina -u example.com --search --dork 'site:example.com inurl:login'
```

Outcome: queries Google and DuckDuckGo with the dork (which must be quoted) and validates the URLs found instead of guessing paths.

### Nmap-assisted guessing

```
cangibrina -u target.example.com -v --nmap
```

Outcome: runs an nmap service scan against the target and uses the detected services and ports to pick likely dashboard locations; a full custom nmap command string can be passed to `--nmap` instead.

*No corpus evidence — derived from upstream documentation.*
