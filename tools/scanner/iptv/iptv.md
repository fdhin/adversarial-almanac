---
name: "iptv"
categories:
  - blackarch-scanner
version: "138.ae6457b"
homepage: "https://github.com/Pinperepette/IPTV"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Pinperepette/IPTV"
github-stars: 427
github-pushed: "2022-03-27"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# iptv

> Deprecated Python 2 research crawler that mines search engines for IPTV streaming portals built on one shared CMS, then leverages a known flaw in that CMS to enumerate the portals' stream URLs.

*No corpus evidence — derived from upstream documentation (Pinperepette/IPTV README; BlackArch installs its two launchers, `iptv-cli` and `iptv-gui`).*

## What it's for

IPTV was written as a demonstration of a supply-chain weakness: large numbers of illegal streaming sites are spun up from the same CMS and even share backend services, so one known CMS exploit yields the stream URLs of many portals at once. The tool crawls search engines for such portals, applies its exploit against each, and collects the resulting stream inventory, with an interactive menu (CLI) or a PyQt interface (GUI). The upstream README marks the project DEPRECATED and is explicit about the legal line: the code demonstrates the technique, and using it to watch redistributed content — or to access third-party services at all — is theft regardless of those services' own legality. Today it is of interest as a study in CMS monoculture risk, and its python2-era dependencies (python2-google, psutil, clint) make it a museum piece that may need pinning to run at all.

## Typical use cases

### Explore the technique via the interactive CLI

```
iptv-cli
```

Outcome: Starts the Python 2 command-line interface (`iptv_cli.py` under the hood) with a menu for searching, exploiting and crawling portals — on an authorized lab box running the affected CMS it demonstrates how one flaw maps to every downstream site.

### Run the PyQt front end

```
iptv-gui
```

Outcome: Opens the graphical version of the same workflow (requires PyQt4 installed), presenting the search/exploit/crawl steps as windows instead of menu prompts.
