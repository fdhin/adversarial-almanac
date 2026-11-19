---
name: "watobo"
categories:
  - web
version: "1.0.1-0kali2"
homepage: "https://sourceforge.net/projects/watobo/"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: ruby
meta-checked: "2026-11-09"
github-repo: "siberas/watobo"
github-stars: 89
github-pushed: "2026-09-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# watobo

> Semi-automated web application security audit proxy in Ruby with a GUI, session management, and built-in vulnerability checks.

## What it's for

WATOBO (Web Application Toolbox, by siberas) is an FXRuby desktop tool that acts as a forwarding proxy between your browser and the target while giving security testers semi-automated auditing. Its differentiators are session management — define login scripts and logout signatures once and it re-authenticates automatically — plus vulnerability checks that work behind anti-CSRF/one-time tokens, inline encode/decode inside the request/response view, smart filters for finding the interesting parts of large sessions, and a plugin/check framework for writing your own tests in Ruby. On Linux it can also run as a transparent proxy via nfqueue. It is GUI-first: the audit workflow happens in the console windows, not on the command line; the repository's `bin/` directory additionally ships helper scripts (`spider.rb`, `sniper.rb`, `watobo_proxy_headless.rb`, `watobo-scanner.rb`) for git/gem checkouts. Documentation and video tutorials live at watobo.sourceforge.net.

## Typical use cases

### Launch the audit proxy console

Start an interactive session against a target.

```
watobo
```

Outcome: opens the FXRuby GUI with its proxy listener; point your browser through WATOBO (or use its transparent mode), log into the target once so the session management captures the login, then run the built-in checks from the UI and review flagged conversations in the session view.

*No corpus evidence — derived from upstream documentation (siberas/watobo README and repository tree).*
