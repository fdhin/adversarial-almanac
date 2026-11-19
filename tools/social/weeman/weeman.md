---
name: "weeman"
categories:
  - blackarch-social
version: "91.53c2efa"
homepage: "https://github.com/Hypsurus/weeman"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-10"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Hypsurus/weeman"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# weeman

> Interactive Python 2 server that clones a login page on your host and logs every credential submitted to it.

## What it's for

Weeman (Hypsurus; last release v1.7.1 "end", 2017) is a classic credential-harvesting web server: point it at a login page, it clones that page's HTML and serves the copy locally, rewriting the form so submitted credentials are written to a local log file before the victim is passed along — for phishing simulation and security-awareness training in a lab you are authorized to test. Everything is configured from an interactive shell (`set url`, `set action_url`, `set port`, `run`); `set action_url` matters because it is where the harvested POST is finally sent, letting the cloned form behave like the original. Two honest limits: it is Python 2 only (SimpleHTTPServer/urllib2, no Python 3 port was ever released) and the author's GitHub account was removed — the packaged snapshot is the final v1.7.1, with evait-security/weeman preserving a public copy of the same source. It does not handle modern login flows that need JavaScript, SSO popups or CAPTCHAs.

## Typical use cases

### Clone a login page and start harvesting

```
weeman
weeman > set url http://example.com/login
weeman > set action_url http://example.com/login
weeman > set port 8080
weeman > show
weeman > run
```

Outcome: after the banner, the four settings are configured and displayed (`show` prints url, port, action_url, user_agent and file options); `run` clones example.com's login page and serves it on port 8080 — every form submission is printed live and appended to `example.com.log` in the working directory (port 80 requires root).

### Launch a configured trap non-interactively from a profile

```
weeman -q -p trap.profile
```

Outcome: with `trap.profile` holding `url = http://example.com/login`, `action_url = http://example.com/login` and `port = 8080` lines (one `key = value` per line), weeman skips the interactive shell (`-q` also suppresses the banner), clones and serves immediately — handy for scripted demonstrations; settings from interactive sessions are likewise remembered in `history.log`.

### Serve a custom lure page instead of a live clone

```
weeman > set html_file lure.html
weeman > run
```

Outcome: instead of cloning `url`, the server hosts your local `lure.html` with the same POST-capture behavior — use it when the target form is behind JavaScript the cloner cannot reproduce.

*No corpus evidence — derived from upstream documentation.*
