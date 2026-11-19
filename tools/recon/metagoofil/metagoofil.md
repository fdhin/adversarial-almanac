---
name: "metagoofil"
categories:
  - blackarch-recon
version: "93.8d50624"
homepage: "https://github.com/opsdisk/metagoofil"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "opsdisk/metagoofil"
github-stars: 605
github-pushed: "2026-10-31"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-09"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# metagoofil

> Finds and downloads the documents a domain has leaked into Google's index.

## What it's for

Metagoofil searches Google for files of chosen types hosted under a target domain (`site:example.com filetype:pdf` style queries) and downloads what it finds — the input stage of document-metadata OSINT. This is the OpsDisk-maintained fork of the classic edge-security tool that BlackArch and Kali package; unlike the original it deliberately does not parse metadata itself, deferring to `exiftool` on the downloaded files. Its main operational constraint is Google: hammering it triggers HTTP 429 blocks, so pace runs with `-e` (delay between searches, default 30s) or route through rotating proxies. It is search-index reconnaissance only — it never touches the target directly. No corpus evidence — derived from upstream documentation.

## Typical use cases

### Harvest documents from a domain

Collect the files a site has gotten indexed.

```
metagoofil -d example.com -t pdf,doc,xls -l 100 -n 20 -o findings
```

Outcome: searches Google for up to 100 results across the listed file types, downloads at most 20 files per type (`-n`) into `findings/` (default `./data`) with 8 download threads (`-r`), and prints the URL inventory as it goes.

### Save the source links without downloading

Keep the map of leaked files for later.

```
metagoofil -d example.com -t pdf -f document-links.txt
```

Outcome: skips heavy downloading and writes every discovered document URL to `document-links.txt` (`-f` with no value defaults to `html_links_<timestamp>.txt`); add `-w` to also download the files behind the links.

### Extract metadata from the harvest

Mine the downloaded files for identity leaks.

```
exiftool -r data/* | egrep -i "Author|Creator|Email|Producer|Template" | sort -u
```

Outcome: the documented companion step — deduplicated author, creator, email and producer values from everything metagoofil saved under `data/`, which is where internal usernames and software versions surface.
