---
name: "googlesub"
categories:
  - blackarch-recon
version: "14.a7a3cc7"
homepage: "https://github.com/zombiesam/googlesub"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "zombiesam/googlesub"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# googlesub

> Python script that scrapes Google search results to discover subdomains of a domain — passive enumeration that never touches the target.

## What it's for

googlesub hunted a domain's subdomains by dorking Google (queries shaped around the target domain) and parsing the hostnames out of the results. Its defining property, per the author's own description, is that it never connects to the site it is researching: all discovery happens against the search engine, so the target sees zero traffic. Its README also warned that repetitive usage triggers CAPTCHAs and degrades results to zero. The upstream repository (zombiesam/googlesub) has been deleted, so no verified command-line interface survives — the archived repository page shows only `googlesub.py` and a short README — and modern Google bot walls would require rework anyway. For the same passive-subdomain goal today, certificate-transparency and code-search based tools are the practical substitutes.

*No corpus evidence — derived from upstream documentation; the repository is gone and its CLI could not be verified, so no example commands are given.*
