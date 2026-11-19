---
name: "social-vuln-scanner"
categories:
  - blackarch-social
version: "11.91794c6"
homepage: "https://github.com/Betawolf/social-vuln-scanner"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Betawolf/social-vuln-scanner"
github-stars: 31
github-pushed: "2016-05-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# social-vuln-scanner

> Scores an organisation's web and social-media presence for the information leaks that make social-engineering attacks easier.

## What it's for

Betawolf's Social Engineering Vulnerability Scanner crawls a company's public web presence (its site plus its Google, Facebook, Twitter and LinkedIn footprint via API keys you supply) and mines names, roles, contact patterns and other employee-revealing content using the Stanford NER named-entity recognizer, then scores the aggregate exposure — essentially a "how attackable are we through our own public information" report. It is a 2016-era research tool: collection runs are slow, the NER server must be launched separately, and the output is a scored findings set for awareness programs rather than an exploitable vulnerability list.

## Typical use cases

### Run a scored exposure scan of an organisation

```
python vuln_scorer.py --gk google.key --fk facebook.key --tk twitter.key --lk linkedin.key example-run https://example.com
```

Outcome: with all four API-key files in place and the Stanford NER server running (launched via `NER.sh`), the tool crawls the homepage and linked social footprint under the label `example-run`, mining and scoring employee-identifying information; results are stored under that run name for the report. Expect a long collection phase — a stable connection is advised.

*No corpus evidence — derived from upstream documentation.*
