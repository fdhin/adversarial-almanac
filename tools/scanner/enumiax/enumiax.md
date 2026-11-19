---
name: "enumiax"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "https://sourceforge.net/projects/enumiax/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-09-14"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "foreni-packages/enumiax"
github-stars: 2
github-pushed: "2015-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# enumiax

> Brute-forces valid usernames on an Asterisk IAX2 VoIP endpoint — sequentially from a character map, or from a dictionary file.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Username enumeration against Inter-Asterisk Exchange (IAX2) services, typically the reconnaissance step before password attacks on VoIP extensions. enumIAX (by Dustin D. Trammell) runs in two modes: sequential guessing, constructing candidate usernames character-by-character from the map defined in `charmap.h` (alphanumerics plus space by default) bounded by minimum and maximum lengths, and dictionary mode from a word file. It adapts its request rate to server feedback (backing off when the server sends lag requests), saves session state at intervals so interrupted runs resume instead of restart, and appends every confirmed username to a `<target>.results` file. It enumerates usernames only — no password guessing, no call placement.

## Typical use cases

### Enumerate usernames sequentially

```
enumiax -m 3 -M 8 198.51.100.10
```

Outcome: brute-forces usernames of 3–8 characters built from the compiled-in character map against the target's IAX2 service; each valid username is printed and appended to `198.51.100.10.results` in the working directory.

### Attack with a username dictionary

```
enumiax -d usernames.txt 198.51.100.10
```

Outcome: replaces generated guesses with one candidate per line from the dictionary file — the right mode when the deployment uses predictable names (surnames, mailbox-style labels, reception accounts).

### Resume an interrupted session

```
enumiax -s 198.51.100.10.state 198.51.100.10
```

Outcome: reloads the saved position from the state file (written automatically at intervals and on termination) and continues the search from where it stopped rather than restarting the keyspace.

### Throttle the request rate

```
enumiax -r 1000000 198.51.100.10
```

Outcome: enforces at least the given delay in microseconds between transactions (default 500000) to avoid flooding the PBX; the tool still self-adjusts — slowing further on server lag requests, speeding up slightly when the server keeps up.
