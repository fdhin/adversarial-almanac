---
name: "usernamer"
categories:
  - blackarch-misc
version: "20.12983f8"
homepage: "https://github.com/jseidl/usernamer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "jseidl/usernamer"
github-stars: 39
github-pushed: "2019-01-07"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# usernamer

> Generates the usernames and logins an organization may plausibly have assigned to a person, ahead of enumeration or brute-forcing.

## What it's for

usernamer turns full names harvested during reconnaissance into candidate
login permutations. Given a name such as "John Doe Doeson" it emits every
conventional form through a plugin system: concatenated (johndoedoeson),
two-term (johndoe), dotted (john.doe), abbreviated (jdoe, johnd), underscore
(john_doe) and progressive prefixes (jd, jod, johd). The point is to replace a
blind username list with a targeted one. It only generates candidates;
enumeration and authentication are left to other tools.

## Typical use cases

### Generate username candidates from a single name

```
usernamer -n "John Doe Doeson"
```

Outcome: prints every permutation of the name across all transformation plugins to stdout, one login per line, ready to pipe into a username-enumeration or brute-force tool.

### Build a wordlist from many names at once

```
usernamer -f names.txt
```

Outcome: reads one full name per line from the file and prints the combined username candidates for all of them.

### Restrict output to the target's login convention

```
usernamer -n "John Doe" -p two_terms,dotted_two_terms -l
```

Outcome: emits only the selected plugins' permutations (here two-term `john.doe`-style forms) forced to lowercase, matching a `firstname.lastname` convention observed on the target's mail addresses.

*No corpus evidence — derived from upstream documentation.*
