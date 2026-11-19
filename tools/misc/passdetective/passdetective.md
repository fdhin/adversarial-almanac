---
name: "passdetective"
categories:
  - blackarch-misc
version: "1.0.6.r4.g458f12e"
homepage: "https://github.com/aydinnyunus/PassDetective"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-21"
build-system: go
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "aydinnyunus/PassDetective"
github-stars: 151
github-pushed: "2026-01-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# passdetective

> Scans shell history files for passwords, API keys and secrets that were typed or pasted by mistake.

## What it's for

PassDetective reads zsh and bash history files and applies regex rules to surface credentials that leaked into commands: passwords passed directly on the command line, cloud API keys (AWS, Google, Stripe, Twilio), private key blocks, Slack tokens, and passwords embedded in URLs. Use it to audit a history file — your own before it gets synced or shared, or a user's during an authorized review — because everything it flags sits in plaintext in `~/.zsh_history` / `~/.bash_history`. It reports matches on the terminal only; it does not edit or purge history entries. The upstream binary is `PassDetective`; on BlackArch it is installed lowercase as `passdetective`.

## Typical use cases

### Audit every shell history on the host

```
PassDetective extract -all
```

Outcome: scans both `~/.zsh_history` and `~/.bash_history` for password-shaped strings and prints a colored console report of each match found.

### Hunt API keys and secrets in zsh history

```
PassDetective extract --secrets --zsh
```

Outcome: applies the full secret-regex set (AWS access keys, Google API keys, Slack tokens, private key headers, credentials in URLs) against `~/.zsh_history` and prints every hit between explicit scan start/finish markers.

### Check one shell only

```
PassDetective extract --bash
```

Outcome: limits the scan to `~/.bash_history`, useful when auditing a single user's shell without touching the rest.

*No corpus evidence — derived from upstream documentation.*
