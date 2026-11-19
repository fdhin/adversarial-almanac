---
name: "protosint"
categories:
  - blackarch-recon
version: "35.34e570a"
homepage: "https://github.com/pixelbubble/ProtOSINT"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "pixelbubble/ProtOSINT"
github-stars: 441
github-pushed: "2026-07-19"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# protosint

> OSINT for Proton Mail: verify whether an address exists, when its PGP key was created, which algorithm it uses, and whether a custom domain routes mail through Proton.

## What it's for

ProtOSINT (v2) investigates Proton Mail accounts. Proton's public key server deliberately returns decoy keys for non-existent addresses, so a naive lookup lies; the tool cross-checks three sources — the key server, the account-availability API, and (most reliably) a Selenium-driven logged-in Proton compose window that validates typed addresses exactly like Proton's own UI. The interactive menu offers four modules: single-address check, username enumeration across all four Proton domains, custom-domain MX check, and batch verification through a real session. On first run it creates a `config.json` (Selenium on/off, headless, optional Proton credentials). Two caveats from the docs: the reported date is the primary PGP key creation date, not necessarily the account creation date, and Proton rate-limits the unauthenticated endpoints — the Selenium path is immune. Authorized/educational use only.

## Typical use cases

### Check a single Proton Mail address

Confirm an address exists and read its key details.

```
protosint
```

Outcome: opens the menu; choose module 1 and enter the address (e.g. `user@protonmail.com`). Prints whether the mailbox exists, the PGP key creation date and the key algorithm (Curve25519, RSA 2048/4096, ...). Custom-domain addresses resolving here imply a paid Proton plan.

### Enumerate a username across every Proton domain

Find which of an organization's aliases actually exist.

```
protosint
```

Outcome: module 2 takes a bare username and checks it against protonmail.com, protonmail.ch, pm.me and proton.me, reporting which addresses are live. Remember `.`/`_`/`-` and `+suffix` variants collapse to the same account, so one hit covers the alias family.

### Check whether a domain uses Proton Mail

Identify mail routing without any mailbox.

```
protosint
```

Outcome: module 3 accepts a domain (e.g. `example.com`), reads its MX records and cross-checks the key server; MX pointing at `mail.protonmail.ch`/`mailsec.protonmail.ch` confirms Proton hosting.

### Batch-verify a list of addresses

Validate many addresses with the authoritative method.

```
protosint
```

Outcome: module 4 feeds a list of addresses through your logged-in Proton session's compose validation — the only fully reliable check — after you configure credentials in `config.json` or at the prompt. Set `use_selenium: false` in `config.json` anywhere you need the faster API-only mode without a browser.

No corpus evidence — derived from upstream documentation.
