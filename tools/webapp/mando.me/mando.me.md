---
name: "mando.me"
categories:
  - blackarch-webapp
version: "9.8b34f1a"
homepage: "https://github.com/z0noxz/mando.me"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "z0noxz/mando.me"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# mando.me

> Web command-injection tool that drives payload delivery through an authenticated page.

## What it's for

mando.me (by z0noxz) automates exploiting command injection in a web page you already have a session on: you point it at the vulnerable URL, supply the session cookie, and mark the injection point with `_INJECT_` in a GET query or POST body template; the tool substitutes and delivers command payloads. Beyond simple output, its README advertised encrypted agent communication, meterpreter/reverse-shell injection, and built-in post-exploitation helpers. It was explicitly labeled under development, the upstream repository was archived by its author in September 2021 and has since been removed — the interface below is documented from the last archived README, and no new development exists to fix issues against hardened targets.

## Typical use cases

### Inject commands through a POST parameter with a session cookie

```
mando.me --url "http://target.example.com/vulnerabilities/exec/" --cookie "PHPSESSID=0123456789abcdef0123456789abcdef" --post "{'submit':'','ip':_INJECT_}"
```

Outcome: sends authenticated POST requests with command payloads substituted at `_INJECT_` in the `ip` field; command output (or the configured agent/reverse-shell session) returns for each successful injection.

No corpus evidence — derived from the last archived upstream README (web.archive.org snapshot of github.com/z0noxz/mando.me, 2023).
