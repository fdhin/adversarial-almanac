---
name: "sawef"
categories:
  - blackarch-webapp
version: "32.e5ce862"
homepage: "https://github.com/danilovazb/sawef"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "danilovazb/sawef"
github-stars: 35
github-pushed: "2021-09-24"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sawef

> Small HTTP swiss-army script: crawl pages for emails/links/forms and replay crafted GET/POST requests at volume.

## What it's for

SAWEF ("Send Attack Web Forms", python2) is a thin HTTP client for
recon-by-crawl and request replay: its crawler module walks a page and
extracts email addresses, links and form definitions, and its request mode
sends GET or POST bodies repeatedly with configurable thread count, custom
User-Agent, Referer and cookies. Reach for it when a one-liner is not enough
but a full proxy or scanner is overkill — enumerating contact addresses,
harvesting endpoints, or stress-testing a form you are authorized to test.
It contains no vulnerability logic: it sends what you tell it and prints the
response element you select (status code, headers, HTML, forms, links or
emails). The upstream has been dormant since 2021 and the code is python2.

## Typical use cases

### Crawl a site for email addresses

Use the crawler module and pick the email response view.

```
sawef --url http://target.example.com/ --modulo crawler --response emails
```

Outcome: crawls the page and prints every email address it finds — contact
enumeration for phishing-aware assessments or username harvesting.

### Replay a POST request with data and cookies

Send a form submission repeatedly with threads and a custom User-Agent.

```
sawef --url http://target.example.com/contact --data '{"name":"user","message":"test"}' --method post --qtd 10 --threads 10 --user_agent '{"User-agent": "Mozilla/5.0"}' --response status_code
```

Outcome: delivers 10 POST requests in parallel with the given JSON body and
header and prints each response's status code — combine `--response html` or
`--response form` to inspect the returned page instead.

*No corpus evidence — derived from upstream documentation (danilovazb/sawef README help text; installed as `/usr/bin/sawef` running sawef.py under python2).*
