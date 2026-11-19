---
name: "cardpwn"
categories:
  - blackarch-social
version: "32.166abf9"
homepage: "https://github.com/itsmehacker/CardPwn"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "itsmehacker/CardPwn"
github-stars: 642
github-pushed: "2023-11-29"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cardpwn

> Interactive OSINT check for whether a payment-card number has been dumped on public paste sites.

## What it's for

You supply a card number (12 to 19 digits); the tool runs search-engine queries pairing each of about 18 paste sites (pastebin, gist.github.com, dpaste, ideone, slexy.org, ...) with that number and prints the URLs of matching pastes along with a total count. Useful as a triage step during breach investigation or card-fraud analysis to see if a specific number is publicly exposed. It is purely a search wrapper: it validates only the digit count, never downloads or parses paste contents, and has no flags or batch mode.

## Typical use cases

### Check a card number for public paste exposure

```
cardpwn
```

Outcome: after a connectivity check the tool prompts `Enter Card No. ->`; entering a 12-19 digit number triggers per-site web searches, and matching paste URLs are printed with a final "Total Dumps Found" count (`No Open Leaks for this Card Number Found.` when nothing matches). Run it per card — there is no file input mode.

*No corpus evidence — derived from upstream documentation.*
