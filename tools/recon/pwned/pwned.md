---
name: "pwned"
categories:
  - blackarch-recon
version: "3063.b6554f1"
homepage: "https://github.com/wKovacs64/pwned"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-05"
build-system: node
license: MIT
meta-checked: "2026-11-09"
github-repo: "wKovacs64/pwned"
github-stars: 246
github-pushed: "2026-11-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 350
gh-activity-checked: "2026-11-09"
---

# pwned

> Node.js command-line client for the Have I Been Pwned? API — breaches per account, pastes, stealer-log lookups, and keyless k-anonymity password checks.

## What it's for

`pwned` puts the whole HIBP v3+ API on one command: breach details, breaches for an account or domain, pastes, stealer-log exposure, subscription info and domain verification. Since 2019 most HIBP endpoints require an API key; the keyless set is `breach`, `breaches`, `dc`/`dcd` (data classes), `lb` (latest breach) and `pw` (password check via k-anonymity, which never transmits the password itself). Configure once with `pwned apiKey` and every protected command works. For a pure Python password-only checker see the sibling `pwned-search` note, and for breach-plus-password-dump searches see `pwnedornot`.

## Typical use cases

### Configure your HIBP API key

Unlock the authenticated commands.

```
pwned apiKey <api-key>
```

Outcome: stores the key locally so subsequent protected commands (`ba`, `bd`, `pa`, `search`, `slbe`, ...) authenticate automatically.

### Check whether a password is publicly exposed

The keyless k-anonymity password check.

```
pwned pw Password1
```

Outcome: sends only five characters of the SHA-1 hash, fetches the matching hash prefix list, and reports how many times the password appears in public breaches — never transmitting the password itself. Zero appearances means it is not in any indexed corpus.

### Check an account's breaches without an API key

Email-address breach lookup using k-anonymity.

```
pwned bar user@example.com
```

Outcome: queries the breached-account endpoint in k-anonymity mode and lists every breach containing that email address, or reports no pwnage found.

### Check an account's breaches with a key

Full account report including pastes and stealer logs.

```
pwned ba user@example.com
```

Outcome: requires an API key; prints all breaches for the account. Follow up with `pwned pa user@example.com` for paste-site appearances or `pwned search user@example.com` to combine breaches and pastes in one report.

### Enumerate breached addresses for a domain

Assess an organization's exposure wholesale.

```
pwned bd example.com
```

Outcome: (API key required, domain must be verified/subscribed) lists every breached email address HIBP has seen on that domain — a quick exposure census for a client's mail namespace.

### Inspect a specific breach

Get the details behind a headline.

```
pwned breach ExampleBreach
```

Outcome: keyless; prints the breach's description, breach date, data classes, pwn count and verification status. `pwned breaches -d example.com` filters the full breach catalog to one domain.

No corpus evidence — derived from upstream documentation.
