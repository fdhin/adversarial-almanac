---
name: "androidpincrack"
categories:
  - blackarch-mobile
version: "5.6439bd2"
homepage: "https://github.com/PentesterES/AndroidPINCrack"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "PentesterES/AndroidPINCrack"
github-stars: 201
github-pushed: "2023-02-10"
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

# androidpincrack

> Python script that brute-forces an Android lockscreen PIN/password offline from the hash and salt of the device's `password.key` — legacy 4.x-era Android only, deprecated upstream.

## What it's for

AndroidPINCrack (PentesterES) attacks the lockscreen secret of a legacy Android device from artifacts pulled off it: the hash stored in `/data/system/password.key` together with its salt. Cracking runs offline on the examiner's machine, which is both faster than entering PINs on the locked device and avoids triggering its lockout/wipe counters. By default it brute-forces 4-digit numeric PINs; `-c` and `-l` widen the charset and maximum length, `-w` switches to a wordlist, and `-m samsung` selects the modified hashing algorithm Samsung shipped. The upstream author archived the repository with an explicit deprecation notice — the script predates 2015 and does not handle modern Android lockscreen storage, so treat it as a forensic tool for old devices and images only; the README itself notes that faster crackers exist for the same hashes.

### Brute-force a 4-digit PIN

Attack the default numeric space.

```
androidpincrack -H DC6831BFE0B8563B82A8AAB9CB5B294BD4B3072A93AF306 -s 7026104367013576733
```

Outcome: iterates numeric PINs up to length 4 against the hash/salt pair and prints `Found! Passcode = <pin>` on a match (the pair above is the upstream demo values); exhausted space without a hit means the PIN is longer or uses a non-numeric charset.

### Attack a Samsung-modified hash

Use the vendor-specific algorithm.

```
androidpincrack -H DC59AACF2AFCE72E737190323022FFB6E2831446 -s 988796901418269782 -m samsung
```

Outcome: hashes candidates with Samsung's modified algorithm instead of the AOSP one and reports the recovered passcode the same way; needed whenever the device is a Samsung of that era, since plain AOSP hashing will never match its hashes.

### Run a wordlist attack

Test candidate secrets from a file instead of enumerating.

```
androidpincrack -H DC59AACF2AFCE72E737190323022FFB6E2831446 -s 988796901418269782 -m samsung -w wordlist.txt
```

Outcome: hashes each line of `wordlist.txt` (one candidate per line) and prints the match — the practical route for alphanumeric passwords; for wider brute force pass `-c '<charset>'` and `-l <max-length>` instead, at a cost of a much larger keyspace.

*No corpus evidence — derived from upstream documentation.*
