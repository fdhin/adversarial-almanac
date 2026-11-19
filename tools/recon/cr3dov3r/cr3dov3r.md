---
name: "cr3dov3r"
categories:
  - blackarch-recon
version: "48.49bbdbe"
homepage: "https://github.com/D4Vinci/Cr3dOv3r"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "D4Vinci/Cr3dOv3r"
github-stars: 2161
github-pushed: "2025-12-09"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# cr3dov3r

> Give it an email address; it digs up public leaks for it and then tests credential reuse across well-known websites.

## What it's for

Cr3dOv3r automates the two halves of a credential-reuse assessment. First it
queries the haveibeenpwned API for breaches containing the address and tries
to recover plaintext passwords from leaked dumps; then it can take a password
(yours or one recovered from a leak) and try it against a list of well-known
websites, reporting which logins succeed and which are blocked by a captcha.
Use it to audit your own exposure or accounts you are explicitly authorized to
test. It is an interactive checker, not a mass-cracking tool: it works one
email at a time, and the site list targets major consumer services rather
than arbitrary targets.

## Typical use cases

### Check an email address against public leaks

Get the full picture for one address in a single run.

```
cr3dov3r user@example.com
```

Outcome: prints every known breach containing the address (breach name, date,
data classes), attempts to recover plaintext passwords from the leaks it
finds, and then interactively offers to test any recovered credentials against
the supported websites, reporting successful logins and captcha walls.

### List breaches without hunting plaintext passwords

Skip the slower plaintext-recovery phase.

```
cr3dov3r -np user@example.com
```

Outcome: shows the haveibeenpwned breach summary for the address but does not
attempt plaintext password lookups, so the run finishes faster and moves
straight on.

### Test a credential you already have

Skip leak lookups entirely and validate one known password.

```
cr3dov3r -p user@example.com
```

Outcome: no breach queries are made; the tool prompts for the password to
test and checks it against the website list, printing per-site login success,
failure, or captcha-blocked results.

No corpus evidence — derived from upstream documentation.
