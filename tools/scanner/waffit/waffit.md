---
name: "waffit"
gentoo-category: "net-analyzer"
version: "9999"
homepage: "http://code.google.com/p/waffit/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "akamajoris/waffit"
github-stars: 1
github-pushed: "2015-04-06"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# waffit

> The original 2009 Google Code "waffit" project — the Python 2 ancestor of wafw00f, installing as `wafw00f.py`.

## What it's for

Waffit is the first public release of the WAFW00F Web Application Firewall detection tool (v0.9.0, 2009, by Sandro Gauci of EnableSecurity and Wendel G. Henrique of Trustwave, BSD-licensed). It sends a benign request and then malicious-looking probes to a URL and matches the responses against WAF signatures. The Pentoo package (net-analyzer/waffit) builds straight from the archived Google Code tree and installs the entry script under its original name `wafw00f.py`, backed by `evillib.py`; the `akamajoris/waffit` GitHub repo linked in the frontmatter is a byte-for-byte mirror of that tree. This is a historical Python 2 tool with a tiny signature set — for any real WAF fingerprinting today use its maintained descendant, [[wafw00f]] (same author, same command surface, hundreds of WAF signatures). The examples below document the legacy tool as packaged.

## Typical use cases

### Fingerprint the WAF in front of a site

```
python2 wafw00f.py http://target.example.com/
```

Outcome: prints the tool banner, probes the target and reports which WAF product (from its small 2009-era signature list) is protecting it, or nothing if no signature matches.

### Report every matching signature instead of stopping at the first

```
python2 wafw00f.py -a http://target.example.com/
```

Outcome: `-a`/`--findall` keeps testing all signatures after the first hit — useful when a site stacks a CDN in front of a WAF and several products both answer.

### Test the host itself rather than a redirect target

```
python2 wafw00f.py -r http://target.example.com/
```

Outcome: `-r`/`--disableredirect` stops wafw00f from following 3xx responses, so the WAF in front of the original host is fingerprinted even when it redirects elsewhere.

### List the WAFs this build can detect

```
python2 wafw00f.py -l
```

Outcome: prints the built-in signature table and exits without sending anything — the quickest way to see the (small) detection coverage before testing.

*No corpus evidence — derived from upstream documentation.*
