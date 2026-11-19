---
name: "rpdscan"
categories:
  - blackarch-cracker
version: "2.a71b0f3"
homepage: "https://github.com/freakyclown/RPDscan"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-01"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "freakyclown/RPDscan"
github-stars: 4
github-pushed: "2019-04-17"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rpdscan

> Recovers saved Remmina connection passwords by decrypting local profile files.

## What it's for

A Python 2 tool by Freakyclown (Cyberis) for auditing credentials stored by
the Remmina remote-desktop client. It walks `/home` for `remmina.pref`
files, extracts each user's `secret=` key, then finds every `*.remmina`
connection profile and decrypts its `password=` entry with 3DES-CBC,
printing the cleartext next to the profile's username, server and domain
lines. It is purely local — nothing touches the network — and it takes no
arguments. Run it as root to reach other users' home directories; profiles
without a saved password are reported as such.

## Typical use cases

### Dump all saved Remmina passwords on the local host

Invoke the tool with no arguments; it discovers configuration and profile
files on its own.

```
sudo rpdscan
```

Outcome: for every `remmina.pref` found, prints its path, then for each
`*.remmina` profile the username, server and domain lines plus the
decrypted `Saved password:` value — a full inventory of Remmina-stored
credentials on the host.

*No corpus evidence — derived from upstream documentation.*
