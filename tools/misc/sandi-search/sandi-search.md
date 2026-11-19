---
name: "sandi-search"
categories:
  - misc
version: "13"
homepage: "https://sourceforge.net/projects/sandisearch/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sandi-search

> Terminal and GUI search front-end across online exploit databases (Exploit-DB, Metasploit, Shell-Storm).

## What it's for

Sandi is a small Python 2 tool by Fardin Allahverdinazhand (0x0ptim0us) that queries three online exploit sources — the Exploit-DB site, the Metasploit exploit tree and Shell-Storm — for a keyword, collects the hits into a single HTML report, and opens it in your browser. It is a quick way to sweep multiple public exploit databases at once for a product or vulnerability keyword and get titles, EDB/CVE/OSVDB identifiers and links on one page. It is a live scraper, not a local database like searchsploit: it needs network access, and because it depends on those sites' 2014-era layouts the scrapers may fail against today's pages. Output location and browser are set in `config/sandi.conf` (`OUTPUT`, default `/tmp/`, and `BROWSER`, default `firefox`); a wxWidgets GUI variant ships alongside the terminal script.

## Typical use cases

### Search Exploit-DB for a keyword

```
sandi -d exploitdb -k wordpress
```

Outcome: queries the Exploit-DB source, writes `result-exploitdb-<session>.html` into the configured OUTPUT directory and opens it in the configured browser; the page lists matching exploits with their identifiers and source links.

### Sweep the Metasploit or Shell-Storm databases

Same search flow against a different database by changing `-d`.

```
sandi -d shell-storm -k bash
```

Outcome: writes `result-shellstorm-<session>.html` (or `result-msf-<session>.html` for `-d msf`) and opens it in the browser; `-d` defaults to `exploitdb` when omitted, `-k` is always required.

*No corpus evidence — derived from upstream documentation.*
