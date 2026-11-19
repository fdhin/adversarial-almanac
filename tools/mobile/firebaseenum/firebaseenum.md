---
name: "firebaseenum"
categories:
  - blackarch-mobile
version: "19.1cbd19c"
homepage: "https://github.com/Sambal0x/firebaseEnum"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Sambal0x/firebaseEnum"
github-stars: 71
github-pushed: "2019-10-15"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# firebaseenum

> Sweep Android app stores — or keyword guesses — for exposed Firebase databases.

## What it's for

firebaseEnum hunts for misconfigured Firebase Realtime Database instances: it
harvests Android APKs from APKpure.com (by category crawl or keyword), extracts
the Firebase database URLs embedded in each app, and checks whether those
databases answer unauthenticated clients. A keyword mode generates candidate
URLs from a company name and a mutation list, testing for exposed databases
without needing an APK at hand. It only reports what Firebase itself permits
anonymous clients to read; it performs no exploitation beyond that read.

## Typical use cases

### Sweep an app category for open databases

Crawl an APKpure category and test every app collected.

```
firebaseenum -a -c finance -p 2
```

Outcome: downloads the first two pages of finance apps, pulls the Firebase URLs
out of each and reports which databases are exposed.

### Hunt exposed databases by keyword

Guess organization-related Firebase URLs from a keyword.

```
firebaseenum -k company
```

Outcome: probes candidate URLs built from the keyword and the mutation list
(`-m`, default `tools/fuzz.txt`; `-k` may be given multiple times) and lists
databases that respond without authentication.

*No corpus evidence — derived from upstream documentation.*
