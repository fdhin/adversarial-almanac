---
name: "outlook-webapp-brute"
categories:
  - blackarch-cracker
version: "1.61d7177"
homepage: "https://github.com/lijiejie/OutLook_WebAPP_Brute"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lijiejie/OutLook_WebAPP_Brute"
github-stars: 65
github-pushed: "2014-05-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# outlook-webapp-brute

> Minimal threaded brute-forcer for Outlook Web App (OWA) logins.

## What it's for

Outlook WebAPP Brute (lijiejie, 2014) tests username/password combinations against a single Outlook Web App host. It takes the site's domain plus user and password dictionary files as positional arguments and tries each pair until valid credentials are found. The packaged command runs the multi-threaded variant (`Outlook_threaded.py`) with a caller-chosen thread count; a single-threaded `Outlook.py` ships alongside it for unstable servers, since the threaded version can hit SSL handshake errors under load (it retries on error). It is a fixed-target Python 2 script with no lockout awareness — for Microsoft 365 tenants use o365spray instead, and treat this tool as a last resort against one on-premises OWA server.

## Typical use cases

### Threaded brute-force run

Credentials from two dictionaries, N workers.

```
outlook-webapp-brute mail.corp.example.com users.txt passwords.txt 20
```

Outcome: 20 threads submit credential pairs from the two dictionaries against the OWA host until the lists are exhausted, printing the username/password pairs that authenticate; lower the thread count if the target starts failing SSL handshakes.

### Single-threaded fallback

For unstable targets.

```
cd /usr/share/outlook-webapp-brute && python2 Outlook.py mail.corp.example.com users.txt passwords.txt
```

Outcome: the same dictionary attack one connection at a time, avoiding the SSL handshake failures the threaded version can trigger on flaky servers, at the cost of speed.

*No corpus evidence — derived from upstream documentation.*
