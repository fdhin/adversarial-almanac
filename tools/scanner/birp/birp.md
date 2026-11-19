---
name: "birp"
categories:
  - blackarch-scanner
version: "65.b2e108a"
homepage: "https://github.com/sensepost/birp"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sensepost/birp"
github-stars: 127
github-pushed: "2021-11-08"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# birp

> BIRP (Big Iron Recon & Pwnage) — a Burp-style assessment proxy for mainframe applications over TN3270: it reveals hidden screen fields, lets you edit "protected" fields, and records every transaction for search and review.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Doing to a mainframe terminal application what an intercepting proxy does for web apps. TN3270 screens routinely hide fields and mark fields protected/non-modifiable, trusting the 3270 emulator not to show or change them; BIRP (by Dominic White, SensePost — the name is a Burp pun, it is not the web proxy) drives a patched x3270 client, prints every returned screen in a marked-up "hacker view" with hidden fields exposed, and allows protected fields to be edited, which depending on the application bypasses developer assumptions about what a user can submit. Every detected transaction (before/after screen plus modified fields) is recorded to a proxy history you can view, search, save and reload, and an embedded IPython console exposes the screen objects for direct interrogation (e.g. `history.last().response.hidden_fields`). Unix-only, python2-era, and needs the bundled x3270 patches for the hidden/protected-field features to fully work.

## Typical use cases

### Proxy a TN3270 session

```
birp -t 198.51.100.10:1023
```

Outcome: opens the target session through BIRP — keypresses pass through to the mainframe while each returned screen is re-printed in the hacker view (hidden fields shown, protected fields editable with the patched x3270), and every transaction is recorded to the in-session history. Ctrl-h prints the help screen, Ctrl-k the color key, ESC returns to the menu. A bare host with no `:port` defaults to port 23.

### Review and replay a saved history

```
birp -t 198.51.100.10:1023 -l saved-history
```

Outcome: loads the previously saved history file into the session, so the View History and Search History menus can inspect past transactions — submitted screens, modified fields, responses — without re-walking the application. A target must still be specified; history cannot be browsed targetless.
