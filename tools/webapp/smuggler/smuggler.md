---
name: "smuggler"
categories:
  - blackarch-webapp
version: "23.2be871e"
homepage: "https://github.com/defparam/smuggler"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "defparam/smuggler"
github-stars: 2119
github-pushed: "2024-01-02"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smuggler

> defparam's HTTP request smuggling (desync) tester — deep single-target probing with pluggable mutation configs.

## What it's for

Smuggler (defparam, Python 3) tests a single web server (or a piped list of servers) for HTTP
request smuggling / desync issues, applying James Kettle's methodology: it sends Transfer-Encoding
and Content-Length mutation gadgets and watches for differential behavior. Mutations live in
Python config files (`config/default.py` plus custom ones), so new gadget shapes are added without
touching the scanner. The README is explicit that results are hints, not verdicts — front-line
processors from large CDNs can produce false positives, and a clean run does not prove absence.
For list-based mass probing of many hosts/URLs, see [[smuggler-py]] (gwen001) — same bug class,
batch-oriented workflow.

## Typical use cases

### Test one target

```
smuggler -u https://target.example.com/
```

Outcome: the default mutation set is sent and each result is annotated (potential issue vs. OK);
`https://` URLs are probed over TLS, `http://` in the clear, and a bare host defaults to HTTPS.

### Sweep a list of servers from stdin

```
cat hosts.txt | smuggler -q -l results.log
```

Outcome: each host in the piped list is scanned in turn; `-q` quiets the output to findings only
and everything (including the quiet findings) is written to `results.log` for review.

### Stop at the first finding and pin the method

```
smuggler -u https://target.example.com/ -m GET -x
```

Outcome: mutations are sent with GET instead of the default POST, and the scan of this host ends
as soon as a potential desync is found — in piped mode the scan simply moves on to the next host.

### Test custom mutation gadgets

```
smuggler -u https://target.example.com/ -c custom.py
```

Outcome: the named config file from the config directory replaces `default.py`, so hand-crafted
header/TE mutations (e.g. obscure whitespace or obfuscation variants) are rendered and tested
against the target.

No corpus evidence — derived from upstream documentation (README).
