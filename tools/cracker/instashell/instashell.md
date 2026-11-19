---
name: "instashell"
categories:
  - blackarch-cracker
version: "44.8f867bd"
homepage: "https://github.com/maxrooted/instashell"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-07"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "maxrooted/instashell"
github-stars: 478
github-pushed: "2022-09-05"
github-archived: false
github-issues: 86
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# instashell

> Multi-threaded Instagram password brute-forcer with Tor-based rate-limit evasion.

## What it's for

A bash brute forcer for Instagram accounts that authenticates through the
Android app's API signature, so it is not bound by the web login's
password-attempt limit, and routes attempts through Tor with automatic IP
rotation to avoid blocking. It runs multi-threaded (20 threads reach roughly
400 passwords per minute), can check whether a username is valid, ships a
built-in password list (~39k entries), and saves every session so interrupted
runs can be resumed later. It needs a running Tor service plus curl and
openssl.

## Typical use cases

### Launch a brute-force run

Start an interactive attack against an account.

```
service tor start
instashell
```

Outcome: prompts for the target username, a password list path (pressing Enter
uses the bundled default list) and a thread count (default 10); the script then
fires password attempts through Tor, rotating the circuit when blocked, prints
the working password on success, and stores progress as a resumable session.

### Resume a saved session

Continue an earlier interrupted run.

```
instashell --resume
```

Outcome: lists the saved sessions for the account, asks which one to continue
and how many threads to use, and picks the attack up from the last untried
password instead of restarting the list.
