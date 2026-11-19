---
name: "ffuf-scripts"
categories:
  - blackarch-misc
version: "1.2192bf7"
homepage: "https://github.com/ffuf/ffuf-scripts"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-05"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ffuf/ffuf-scripts"
github-stars: 228
github-pushed: "2023-12-24"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ffuf-scripts

> Payload-generation helpers from the ffuf project that stream straight into ffuf over stdin.

## What it's for

A small collection of shell scripts that prepare payload types ffuf's plain wordlists cannot express and hand them to ffuf on stdin (`-w -`). The shipped `ffuf_basicauth.sh` cross-joins a username wordlist and a password wordlist into every `username:password` pair, base64-encodes each pair, and emits one credential per line. The scripts are generators only — requesting, matching and response filtering remain ffuf's job.

## Typical use cases

### Brute-force HTTP Basic authentication

Feed every username/password combination to ffuf as the `AUTH` keyword inside an `Authorization: Basic` header.

```
/usr/share/ffuf-scripts/ffuf_basicauth.sh usernames.txt passwords.txt | ffuf -w -:AUTH -u https://example.org/endpoint -H "Authorization: Basic ***" -fc 403 -c
```

Outcome: ffuf iterates the streamed combinations, substituting each base64-encoded `username:password` where `***` stands; responses with status 403 are filtered out and hits print in color. Swapping `-fc 403` for `-mc 200` keeps only successful authentications.

*No corpus evidence — derived from upstream documentation.*
