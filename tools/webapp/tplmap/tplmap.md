---
name: "tplmap"
categories:
  - blackarch-webapp
version: "719.616b0e5"
homepage: "https://github.com/epinna/tplmap"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "epinna/tplmap"
github-stars: 4215
github-pushed: "2024-04-21"
github-archived: false
github-issues: 46
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tplmap

> Automatic server-side template injection (SSTI) detection and exploitation with sandbox escapes.

## What it's for

Tplmap (epinna) assists the detection and exploitation of code injection and Server-Side
Template Injection, with sandbox-escape techniques to reach the underlying operating
system. It probes a parameter across more than 15 template engines and eval-like code
contexts (Jinja2, Mako, Tornado, Python eval, Nunjucks, Pug, doT, Marko, EJS, Slim, ERB,
Ruby eval, Smarty, Twig <=1.19, Freemarker, Velocity, PHP eval), identifies the engine and
injection technique — including blind scenarios — and reports the capabilities of the
injection point (shell command execution, bind/reverse shell, file read/write, code
evaluation). It is the sqlmap-shaped standard for SSTI. The project is no longer
maintained, so newer engines and versions (e.g. Twig >1.19, secured Smarty) are out of
scope.

## Typical use cases

### Detect and fingerprint an injection point

Run it against a URL to confirm SSTI and identify the engine.

```
tplmap -u 'http://target.example.com/page?name=John'
```

Outcome: tests the GET parameter across all engine plugins and prints the confirmed injection point — engine, injection tag, context, OS, technique, and capabilities (command execution, file read/write, code evaluation) — plus the exploitation options to rerun with.

### Get an operating-system shell

Escalate the confirmed injection to a pseudo-terminal.

```
tplmap --os-shell -u 'http://target.example.com/page?name=John'
```

Outcome: drops into a shell prompt on the target (`whoami`, `cat /etc/passwd` work directly); `--os-cmd` runs a single command instead, and `--bind-shell PORT`, `--reverse-shell HOST PORT`, `--upload`/`--download` cover the other post-exploitation actions from the capability report.

No corpus evidence — derived from upstream documentation.
