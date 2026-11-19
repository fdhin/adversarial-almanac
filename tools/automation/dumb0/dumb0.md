---
name: "dumb0"
categories:
  - blackarch-automation
version: "19.1493e74"
homepage: "https://github.com/0verl0ad/Dumb0"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "0verl0ad/Dumb0"
github-stars: 31
github-pushed: "2018-01-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dumb0

> Harvest valid usernames from a forum or CMS by walking its numeric user-ID profile pages.

## What it's for

dumb0 enumerates user accounts on popular forum and CMS platforms — WordPress, SMF, vBulletin, Invision Power Board, XenForo, myBB, Drupal, Moodle, and about twenty more (27 supported) — by requesting each numeric user-ID profile URL and extracting the username that comes back. It is the reconnaissance step before a targeted brute-force or credential-stuffing run: it hands you the valid username list instead of making you guess. It needs the platform to expose ID-based profile pages, supports an optional session cookie for member-only forums, and interactively asks what text appears before and after a sample nickname so it can trim each result down to the clean username.

## Typical use cases

### List supported platforms

Find the type code for your target's software.

```
dumb0 --list
```

Outcome: prints the table of supported forums/CMS with their codes (`WP`, `SMF`, `VB`, `IPB`, `XEN`, `MOODLE`, ...) to feed to `--type`.

### Dump usernames from a public forum

Enumerate the user base of an open platform.

```
dumb0 --url https://target.example.com --type VB
```

Outcome: fetches a first profile to verify the URL works, interactively asks for the text appearing before and after the nickname, then walks the user IDs and prints every harvested username to stdout — redirect to a file to keep the list.

### Dump from a forum that requires a session

Enumerate a platform that hides profiles from anonymous visitors.

```
dumb0 --url https://target.example.com --type SMF --cookie 'PHPSESSID=abcdef0123456789'
```

Outcome: the same enumeration with the session cookie attached to every request, so member-only profile pages are returned and parsed.

*No corpus evidence — derived from upstream documentation.*
