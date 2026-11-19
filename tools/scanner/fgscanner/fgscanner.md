---
name: "fgscanner"
categories:
  - blackarch-scanner
version: "11.893372c"
homepage: "http://www.fantaghost.com/fgscanner"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "FantaGhost/FGscanner"
github-stars: 7
github-pushed: "2014-04-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fgscanner

> FantaGhost's Perl web content scanner (`fgscan`): dictionary-discovers hidden directories and pages on a web server, with proxy rotation, TOR routing and request-pace controls built in.

*No corpus evidence — derived from upstream documentation.*

## What it's for

fgscan finds content that is present but unlinked by running dictionary attacks against a hostname: every entry of a directory list and a page list (the shipped `fg_dirs.txt` and `fg_pages.txt`) is requested and the responses classified. The distinguishing feature set is evasion and pacing: requests can be spread across a list of proxies, routed through TOR (optionally resolving DNS through TOR too), issued with a randomized User-Agent per request, and spaced out with a delay between requests. It takes a bare hostname — no `http://` scheme — and does not exploit anything it finds; it enumerates what exists. Note that the script's own `--help` text mentions `--dirlist`/`--wordlist`, but the option parser actually accepts `--dirs` and `--pages`; the examples here use the parsed forms.

## Typical use cases

### Dictionary-scan a host with the shipped lists

```
fgscan --host target.example.com
```

Outcome: the tool walks the bundled directory and page wordlists against the host and reports discovered paths; add `--dump` to also save the content of found pages to disk.

### Scan with custom lists and human-like pacing

```
fgscan --host target.example.com --dirs dirs.txt --pages pages.txt --sec 999
```

Outcome: the scan uses your dictionaries, and `--sec 999` randomizes the delay between requests from 1 to 30 seconds — a fixed value like `--sec 5` spaces requests deterministically instead.

### Route the whole scan through TOR

```
fgscan --host target.example.com --tor --tordns --uarnd
```

Outcome: every request exits through TOR, and with `--tordns` hostname resolution also happens inside the TOR network rather than leaking to the default resolver; `--uarnd` gives each request a random User-Agent from the shipped list.

### Distribute the scan across a proxy list

```
fgscan --host target.example.com --proxy proxies.txt
```

Outcome: requests are cycled through the proxies listed in the file instead of arriving from a single source address; the tool verifies TOR availability when `--tor` is set and debug output (`--debug`) shows which list files and settings are active.
