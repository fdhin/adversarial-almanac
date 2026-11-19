---
name: "dumpzilla"
categories:
  - blackarch-forensic
version: "03152013"
homepage: "http://www.dumpzilla.org/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Busindre/dumpzilla"
github-stars: 149
github-pushed: "2025-12-09"
github-archived: false
github-issues: 10
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# dumpzilla

> Extract every forensically interesting artifact from a Firefox/Iceweasel/SeaMonkey profile (discontinued upstream).

## What it's for

Dumpzilla (Busindre) is a command-line extractor for Mozilla-browser
profiles. Given a profile directory it parses and reports cookies
(including HTML5 DOM storage), browsing history, bookmarks, downloads,
saved form data (searches, emails, comments), preferences and
permissions, addons, saved passwords, certificate exceptions, session
data, the offline cache, and visited-site thumbnails. Each section
takes filters — string wildcards (`%` for any run of characters, `_`
for one character), regular expressions via `--RegExp`, and date or
visit ranges — and output is plain text meant to be piped through
grep/awk/cut. Every run prints the SHA256 hash of each file it read
plus a closing summary of totals. The project is discontinued (last
release 2013) and targets the profile layout of that browser era, so
verify its findings against current Firefox profiles before relying on
them.

## Typical use cases

### Dump a whole profile

Process every section at once for a first triage.

```
dumpzilla ~/.mozilla/firefox/abc3de9u.default
```

Outcome: all sections (cookies, history, forms, passwords, ...) are
printed in turn, ending with the SHA256 hash of each profile file read
and a summary count per section.

### Filter cookies by domain, with DOM storage

Narrow the cookie section to a target site.

```
dumpzilla ~/.mozilla/firefox/abc3de9u.default --Cookies -showdom -domain '%example.com%'
```

Outcome: only cookies whose domain matches the wildcard are listed, and
`-showdom` adds the HTML5 DOM storage entries so web- and
tracking-related data appear together.

### Rank history entries by frequency

Show which URLs the user actually visited most.

```
dumpzilla ~/.mozilla/firefox/abc3de9u.default --History -url '%login%' -frequency
```

Outcome: history records whose URL matches the wildcard, with a
visit-frequency breakdown — useful to spot repeated logins to a
specific service.

### Recover the browser's saved passwords

Pull credential material held by the profile.

```
dumpzilla ~/.mozilla/firefox/abc3de9u.default --Passwords
```

Outcome: the passwords stored by the browser's password manager for
that profile are listed (decryption uses the profile's own NSS key
material, hence the `nss` dependency of the package).

### Export everything as JSON

Get machine-readable output for scripted processing.

```
dumpzilla ~/.mozilla/firefox/abc3de9u.default --Export json-out
```

Outcome: the extracted data is written as JSON files into `json-out/`
instead of (only) text, ready for ingestion by other tooling.

### Watch browsing activity in real time

Observe a live session on a running browser profile.

```
dumpzilla ~/.mozilla/firefox/abc3de9u.default --Watch -text 'password'
```

Outcome: daemon mode (Unix only) prints the URL of each page visited
and the text entered in forms as it happens; `-text` filters the form
text, e.g. to catch a chosen keyword while it is typed.

*No corpus evidence — derived from upstream documentation.*
