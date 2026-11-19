---
name: "shortfuzzy"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://packetstormsecurity.com/files/104872/Short-Fuzzy-Rat-Scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/shortfuzzy"
github-stars: 0
github-pushed: "2019-01-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shortfuzzy

> Interactive Perl path fuzzer that combines a hosts list with a fuzz wordlist (2011-era).

## What it's for

Short Fuzzy Rat (by ratdance, inspired by ikki's 879-vector fuzzing list) is a small interactive
Perl script from 2011: you give it a file of hosts and a file of paths, and it requests
`http://HOST/FUZZ/` for every combination, printing and logging responses that succeed. It was
written as a learning aid for web fuzzing and shows its age — no threading, no rate control, no
status-code filtering beyond "response succeeded". Use it for quick lab exercises or as a minimal
reference implementation of hosts-x-wordlist fuzzing; for real discovery work a modern fuzzer
(ffuf, feroxbuster) supersedes it. The packaged command wraps `short.fuzzy.rat.pl`; the bundled
`Fuzzer.txt` wordlist ships in `/usr/share/shortfuzzy/`.

## Typical use cases

### Fuzz a list of hosts with a wordlist

Launch the wrapper and answer the three prompts.

```
shortfuzzy
```

Outcome: the script asks for a hosts-list file path, a fuzz-list file path, and an HTTP method
(e.g. `GET` or a HTTP/1.0 variant). It then queries each `http://HOST/FUZZ/` combination, prints
the decoded body of every successful response, and appends each hit with a timestamp to a `Log`
file in the current directory; a completion banner marks the end. Using
`/usr/share/shortfuzzy/Fuzzer.txt` as the fuzz list reproduces the original packaged workflow.

No corpus evidence — derived from the packaged upstream source (short.fuzzy.rat.pl) and the BlackArch packaging.
