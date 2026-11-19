---
name: "pipeline"
categories:
  - blackarch-cracker
version: "19.f4935c9"
homepage: "https://github.com/hirnschallsebastian/Pipeline2"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-04-27"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hirnschallsebastian/Pipeline2"
github-stars: 5
github-pushed: "2021-04-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pipeline

> Streams targeted password candidates from base dictionaries by appending or prepending charset combinations and capitalization variants.

## What it's for

Pipeline is a wordlist mangler for targeted brute-force attacks. It reads one or more input dictionaries and prints every word unmodified, with capitalization rules applied (`-capFirst`, `-capAll`, `-capLast`, `-capAllButFirst`, `-capAllButLast`, or all of them via `-capCeption`), and combined with every possible string of up to `-max` characters from a charset, appended or prepended — so a short base list yields guesses like `Password`, `password123` or `!admin`. It performs no cracking itself: candidates go to stdout and are meant to be piped into a cracker's stdin. Reach for it when the password is suspected to be a known base word plus a short suffix or prefix; it is not a general rules engine and cannot reduce or filter existing wordlists.

## Typical use cases

### Pipe mangled dictionary words into John the Ripper

Combine two dictionaries, capitalize the first letter, and append up to three charset characters while a John session reads the stream.

```
pipeline -w rockyou.txt -w company-terms.txt -capFirst -append -max 3 | john --stdin --format=NT hashes.txt
```

Outcome: each base word is printed alone (both lowercase and first-letter-capitalized) and with every appended combination of 0–3 charset characters; John consumes the stream on stdin and reports any candidate that matches the NT hashes in `hashes.txt`.

### Prepend digits from a custom charset to a name list

Build a ready-to-use wordlist of names prefixed with short numeric strings.

```
pipeline -w names.txt -prepend -min 1 -max 2 -charSet 0123456789 > candidates.txt
```

Outcome: `candidates.txt` receives every capitalization variant of each name alone and with all 1–2 digit prefixes (e.g. `89anna`), usable with any cracker's `-w`/wordlist option.

### Run the mangling multithreaded for large inputs

Keep the generator fed across several threads with a large per-thread buffer.

```
pipeline -w employees.txt -t 8 -b 100000 -capAll -append -max 4 -charSet 0123456789!?
```

Outcome: the same candidate stream as the single-threaded run, generated on 8 threads with 100000 values buffered per thread, noticeably faster on multi-core hosts when the appended string length grows.

*No corpus evidence — derived from upstream documentation.*
