---
name: "ezxss"
categories:
  - webapp
version: "git (2026-11-05)"
homepage: "https://github.com/ssl/ezxss"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "ssl/ezxss"
github-stars: 2352
github-pushed: "2026-11-05"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 5
---

# ezxss

> A self-hosted blind XSS platform: deploy the panel, plant its payload, and collect full reports whenever it fires.

## What it's for

ezXSS (by GitHub user ssl) is a PHP web application you host yourself, aimed at penetration testers and bug bounty hunters testing (blind) cross-site scripting. After deployment the dashboard hands you a JavaScript payload snippet; inject it wherever a blind XSS might live (stored profile fields, admin panels, contact forms). When a victim's browser executes it, the panel stores a report — page URL, IP, referer, user agent, cookies, local and session storage, the full DOM, and a screenshot — and can alert via email, Telegram, Slack, Discord or a callback URL. Payloads can be made persistent to track a victim across pages, and an extension system adds custom collection behavior. It is a web panel plus payload service with no command-line interface: management happens entirely in the dashboard after deployment, so there are no runnable commands to document here. A hosted version is available at ezxss.com for trying it without running a server.

No corpus evidence — derived from upstream documentation.
