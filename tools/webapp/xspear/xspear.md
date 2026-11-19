---
name: "xspear"
categories:
  - blackarch-webapp
version: "1.4.1.r10.gf7bab38"
homepage: "https://github.com/hahwul/XSpear"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-07"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "hahwul/XSpear"
github-stars: 1357
github-pushed: "2026-03-03"
github-archived: true
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# xspear

> XSS scanner and parameter analyzer, distributed as a Ruby gem, with Selenium-verified payload execution.

## What it's for

XSpear tests a URL's parameters for cross-site scripting in stages: it first analyzes how each parameter is reflected (filtered characters, reflection patterns, SQL error patterns leaking in responses), then injects progressively stronger payloads and — when a browser is available — confirms actual execution by checking for alert/prompt/confirm dialogs through Selenium, rather than trusting raw reflection. It supports testing all parameters including non-reflected ones, blind XSS callbacks, custom payload JSON files, raw request files, and JSON output for pipelines. The repository is archived and the gem is no longer maintained, so expect it to be installed as-is from the last release. It is a single-target scanner, not a crawler or proxy.

## Typical use cases

### Scan one endpoint with detailed logs

Check a single reflective parameter and watch the per-request reasoning.

```
xspear -u "http://target.example.com/search.php?test=query" -v 2
```

Outcome: XSpear analyzes the request, generates a test query set, and streams log lines marking findings by severity — `[I]` info (e.g. reflected parameters, SQL error patterns), `[H]` high (reflected XSS code), `[V]` vulnerable (Selenium-confirmed alert/prompt/confirm) — ending with a summary report table.

### Test a POST parameter

Pass the body explicitly when the sink is a form submission.

```
xspear -u "http://target.example.com/search.php" -d "searchFor=yy" -v 1
```

Outcome: the POST body parameter is analyzed and tested instead of the query string, with the same severity-graded report at the end.

### Hunt blind XSS across every parameter

Inject a callback host into all parameters, including ones that never reflect.

```
xspear -u "http://target.example.com/profile.php?id=1" -b "https://198.51.100.10" -a
```

Outcome: every parameter (not just reflected ones) is seeded with a blind-XSS vector pointing at the callback host; any payload that executes in a victim or admin view calls back there. `-b` integrates with hosted platforms such as XSS Hunter or ezXSS.

### Analyze parameters without XSS testing

Run only the reflection/filter analysis pass.

```
xspear -u "http://target.example.com/search.php?test=query" --no-xss
```

Outcome: a fast parameter-only report (reflections, filtered characters, interesting error patterns) with no payload injection — useful reconnaissance before choosing payloads by hand.

### Produce machine-readable output for pipelines

Silence the console log and emit JSON.

```
xspear -u "http://target.example.com/search.php?test=query" -o json -v 0
```

Outcome: prints only the JSON result document (start/end time, issue count, issue list with severity per finding), which can be piped into jq or a reporting pipeline.

No corpus evidence — derived from upstream documentation.
