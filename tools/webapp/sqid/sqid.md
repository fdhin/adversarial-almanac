---
name: "sqid"
categories:
  - blackarch-webapp
version: "0.3"
homepage: "http://sqid.rubyforge.org/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-19"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/sqid"
github-stars: 0
github-pushed: "2019-01-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sqid

> SQL Injection Digger: a Ruby command-line scanner that hunts SQL injection signatures in URLs.

## What it's for

SQID (SQL Injection Digger, v0.3, Metaeye Security Group) is an old-school command-line
program that looks for SQL injections and common database error signatures in web sites.
It can check a single URL or a file of URLs, check the links extracted from one page, or
crawl a whole site and check as it goes. Detection is signature-based: a trigger string
(default `'`) is injected into parameters and responses are matched against regex
signatures in `sqid.db`, which you can extend with your own signature files. It supports
HTTPS, proxies with authentication, basic auth, cookies, custom user-agent and referer.
Its google-search mode reflected the pre-API scraping era and is not practical today. The
tool predates modern WAF-era JavaScript-heavy apps, so treat it as a lightweight classic
scanner rather than a modern SQL injection framework.

GitHub: third-party mirror of the upstream SQID 0.3 release (rubyforge/PacketStorm).

## Typical use cases

### Check a single URL or a file of URLs

Inject triggers into one URL's parameters and match responses against the signature database.

```
sqid -m u -u "http://target.example.com/page.php?id=1"
```

Outcome: reports parameters whose responses match SQL injection/error signatures from `sqid.db`. Passing a file path instead of a URL checks every URL listed in the file (one per line).

### Crawl a site and check every page found

Walk the target site and test each discovered link.

```
sqid -m c -c http://target.example.com
```

Outcome: crawls the site, applies the trigger to parameters of each page crawled, and prints any signature matches as it goes.

### Test with custom triggers and signatures

Extend detection beyond the default single-quote trigger.

```
sqid -m u -u "http://target.example.com/search?q=test" -t triggers.txt -D custom-signatures.db
```

Outcome: every trigger in `triggers.txt` (one per line, `#` comments ignored) is used against the parameter, and responses are matched against both the default and the custom signature databases.

No corpus evidence — derived from upstream documentation (packaged readme and sqid.rb).
