---
name: "pagodo"
categories:
  - blackarch-scanner
version: "166.3228879"
homepage: "https://github.com/opsdisk/pagodo"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "opsdisk/pagodo"
github-stars: 3414
github-pushed: "2026-11-01"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# pagodo

> Automates Google dork searches to find vulnerable web pages and indexed exposures.

## What it's for

pagodo (Passive Google Dork, opsdisk) replaces manual Google dorking: it takes a list of Google dorks — typically scraped from the Exploit-DB Google Hacking Database (GHDB) — runs each as a Google search and collects the resulting URLs, optionally scoped to a single domain. Two components: `ghdb_scraper.py` refreshes the dork lists, and `pagodo.py` executes them, with randomized delays between searches to mimic a human, HTTP(S)/SOCKS5 proxy round-robin support, and text/JSON result output. The project was archived in November 2026 after Google changed how search results are delivered and the underlying search library became unreliable — expect breakage. Scraping Google also falls outside their Terms of Service. On distro installs the console is `pagodo`, with `ghdb_scraper.py` and the `dorks/` data under `/usr/share/pagodo/`.

## Typical use cases

### Refresh the dork lists from GHDB

Fetch the current Google Hacking Database entries before a dorking run.

```
python3 /usr/share/pagodo/ghdb_scraper.py -s -j -i
```

Outcome: writes `all_google_dorks.txt` (one dork per line), `all_google_dorks.json` and per-category dork files (Footholds, Vulnerable Files, File Containing Passwords, ...) into the dorks directory, ready to feed pagodo.

### Dork a single domain

Check what dork-type exposures Google has indexed for one target.

```
pagodo -d example.com -g dorks.txt
```

Outcome: runs every dork from the file as a Google search scoped to example.com, with randomized delays (default 37-60 seconds) between queries, and prints matched URLs grouped per dork.

### Collect results to files with proxy support

Save findings and route searches through proxies.

```
pagodo -g all_google_dorks.txt -s -o -p socks5://127.0.0.1:9050,http://127.0.0.1:8080
```

Outcome: matched URLs are saved both to a plain text file (`-s`) and a richer JSON file (`-o`), while searches go out round-robin over the comma-separated proxies (HTTP(S) or SOCKS5) — no external wrapper like proxychains needed.

No corpus evidence — derived from upstream documentation (opsdisk/pagodo README plus the pagodo.py/ghdb_scraper.py argument tables).
