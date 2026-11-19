---
name: "wikigen"
categories:
  - blackarch-automation
version: "8.348aa99"
homepage: "https://github.com/zombiesam/wikigen"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: WTFPL
meta-checked: "2026-11-09"
github-repo: "zombiesam/wikigen"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# wikigen

> Wikipedia wordlist generator — crawls a wiki page and builds a wordlist from the words it finds; upstream is gone.

## What it's for

wikigen (by zombiesam) is a small Python script whose entire surviving description, preserved via the Internet Archive, is "Wikipedia Wordlist Generator — will crawl said site and create a wordlist out of the words it find". Its purpose is targeted wordlist generation for password guessing against people or organizations whose public footprint includes Wikipedia coverage: harvest the article text, split it into words, and use the result as a custom dictionary. Honest status: the upstream GitHub repository has disappeared (`github-status: gone`), only the repo's two-line README is archived, and no command-line options survive anywhere verifiable. What is known from the BlackArch packaging: the script was `wwg.py`, installed as `wikigen`, and depended on Python 2 — a pre-2020 codebase by today's standards. Do not expect it to run unmodified on a modern Python 3-only system, and prefer maintained alternatives (e.g. CeWL for scraping arbitrary sites, or cewl/wiki-to-wordlist approaches) for current work. This note intentionally contains no usage examples rather than inventing an unverifiable CLI.

*No corpus evidence — upstream repository deleted; description reconstructed from the archived README and BlackArch packaging metadata.*
