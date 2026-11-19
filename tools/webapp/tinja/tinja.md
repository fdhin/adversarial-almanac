---
name: "tinja"
categories:
  - blackarch-webapp
version: "1.2.0.r5.g507d903"
homepage: "https://github.com/Hackmanit/TInjA"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: go
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "Hackmanit/TInjA"
github-stars: 427
github-pushed: "2026-04-27"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# tinja

> TInjA — Template INJection Analyzer: polyglot-based SSTI/CSTI detection across 44 engines.

## What it's for

TInjA (Hackmanit) is a CLI tool for testing web pages for template injection
vulnerabilities. It automatically detects whether a page is injectable and identifies the
template engine in use, covering 44 of the most relevant engines across eight languages
(Jinja2, Django, Mako, Tornado, Twig, Smarty, Freemarker, Velocity, Nunjucks, Pug, Razor,
Liquid, ERB, and more). It detects both server-side (SSTI) and, with a headless browser,
client-side (CSTI) injection. Efficiency comes from polyglots — on average only five
polyglots are sent until the injection and engine are identified. Input can be URLs, a
file of URLs, a raw HTTP request file, or JSONL (e.g. crawled requests); headers, cookies,
and POST bodies are settable, traffic can be routed through a proxy, and a rate limit can
be enforced. It is a detection/identification tool, not an exploiter.

## Typical use cases

### Scan a URL for SSTI

Check a single page for server-side template injection.

```
tinja url -u "http://target.example.com/?name=Kirlia"
```

Outcome: sends a small set of polyglots to the parameter and reports whether template injection is possible and which template engine is in use. Multiple `-u` flags scan several URLs at once; `-u "file:/path/to/urls.txt"` scans a URL file.

### Scan a POST form with a session

Test an authenticated endpoint that takes POST parameters.

```
tinja url -u "http://target.example.com/profile" -d "name=Kirlia&bio=hello" -c "PHPSESSID=ABC123" -H "X-Custom: 1"
```

Outcome: the POST body, cookie, and extra headers are attached to every probe; the verdict (injectable or not, engine identified) is printed per URL.

### Test a raw HTTP request

Reuse an exact captured request instead of rebuilding options.

```
tinja raw -R request.txt
```

Outcome: the request file (method, path, headers, body) is replayed with polyglots injected; `tinja jsonl -j requests.jsonl` processes a crawled-request JSONL file the same way.

### Scan for client-side template injection too

Extend detection to CSTI in browser-side engines.

```
tinja url -u "http://target.example.com/" --csti --reportpath /tmp/tinja-report
```

Outcome: a headless browser additionally executes the page and flags client-side template injection (higher CPU/RAM); `--reportpath` writes an incrementally updated JSONL report of findings.

No corpus evidence — derived from upstream documentation.
