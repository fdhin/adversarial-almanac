---
name: "didier-stevens-suite"
categories:
version: "448.7d625d0"
homepage: "https://github.com/DidierStevens/DidierStevensSuite"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "DidierStevens/DidierStevensSuite"
github-stars: 2575
github-pushed: "2026-10-13"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---

# didier-stevens-suite

> Umbrella package bundling all of Didier Stevens' security tools — the whole collection in one repository and one shared directory.

## What it's for

Didier Stevens (blog.didierstevens.com) publishes his tools as individual Python scripts — PDF and Office document analysis, malware and unpacking helpers, Cobalt Strike parsers, and small utilities. This package tracks his `DidierStevensSuite` repository, which collects all of them (Python tools at the top level, plus compiled Linux/Windows helper binaries), and installs everything under `/usr/share/didier-stevens-suite/`. There is no global launcher: each tool is a standalone script run by interpreter from that directory, and most print their own manual page with option `-m`. Every major tool is also packaged separately in BlackArch with a proper `/usr/bin` wrapper.

This note is the hub — the individual tools are documented in their own notes: [[pdfid]], [[pdf-parser]], [[make-pdf]], [[zipdump]], [[oledump]], [[jpegdump]], [[base64dump]], [[xorsearch]], [[xorstrings]], [[disitool]], [[emldump]], [[naft]] and [[analyzepesig]]. Beyond those, the suite carries one-off scripts worth knowing exist: `1768.py` (Cobalt Strike beacon config parser), the `cs-*` Cobalt Strike traffic/key parsers, `cut-bytes.py`/`format-bytes.py`, `re-search.py` and `virustotal-search.py`.

## Typical use cases

### Run any suite tool from the shared directory

```
python3 /usr/share/didier-stevens-suite/oledump.py suspicious.doc
```

Outcome: runs the suite copy of oledump against a suspicious Office file without installing its standalone package — the same pattern works for every script in the directory (`pdfid.py`, `pdf-parser.py`, `base64dump.py`, `zipdump.py`, `xorsearch.py`, ...), each with its own options.

### List the toolbox and read a tool's built-in manual

```
ls /usr/share/didier-stevens-suite/ | grep '\.py$' | head
python3 /usr/share/didier-stevens-suite/pdfid.py -m | head
```

Outcome: enumerates the Python tools shipped in the suite, then prints the top of pdfid's embedded manual page — option `-m` is the fastest way to discover what any given suite tool supports without hunting for its blog post.

*No corpus evidence — derived from upstream documentation.*
