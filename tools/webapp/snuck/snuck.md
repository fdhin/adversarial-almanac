---
name: "snuck"
categories:
  - blackarch-webapp
version: "6.76196b6"
homepage: "https://github.com/mauro-g/snuck"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mauro-g/snuck"
github-stars: 90
github-pushed: "2015-08-20"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snuck

> Automated XSS filter bypass that tailors injections to the reflection context, driven in a real browser via Selenium (Java, unmaintained since 2013).

## What it's for

snuck (Mauro Gentile, Sapienza University of Rome, 2012-2013) attacks a specific XSS filter by
specializing payloads for the exact reflection context: a real browser is driven through Selenium,
the injected vector's landing point in the DOM is inspected, and obfuscated vectors are chosen
(and iteratively adapted) to slip past the filter. Multi-step scenarios — login, navigate, fill a
form — are described in XML configuration files of Selenium commands, which is what lets it test
stored/reflected XSS behind workflows rather than single GET parameters. Development stopped in
2013: the jar predates modern Selenium/browsers, so expect compatibility work before it runs
against a current Firefox/Chrome. It is a research-grade single-purpose tool, not a general XSS
scanner.

## Typical use cases

### Quick reflected-XSS test of one parameter

```
snuck -reflected http://target.example.com/search?q=test -p q -report report.html
```

Outcome: the given GET parameter is injected in place, the browser observes the reflection
context, and the HTML report states whether the filter was bypassed and with which vector — no
XML configuration needed for this mode.

### Test an XSS filter behind a login and workflow

```
snuck -start login.xml -config injection.xml -report report.html -d 500
```

Outcome: the commands in `login.xml` authenticate (a Selenium command sequence), `injection.xml`
drives the steps to the injection point, and vectors are tried with a 500 ms delay between
injections; the report lists which tags/attributes the filter allows and any successful bypass.

### Route the browser through a proxy

```
snuck -config injection.xml -report report.html -proxy 127.0.0.1:8080
```

Outcome: the driven browser's traffic flows through the given proxy, so every step and
reflection can be observed in an intercepting proxy while the tool works.

No corpus evidence — derived from upstream documentation (README and the project wiki tutorial).
