---
name: "sucrack"
categories:
  - blackarch-cracker
version: "1.2.3"
homepage: "http://labs.portcullis.co.uk/application/sucrack"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2024-02-01"
build-system: autotools
license: custom
meta-checked: "2026-11-09"
github-repo: "hemp3l/sucrack"
github-stars: 101
github-pushed: "2019-05-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sucrack

> Multithreaded local su() brute-forcer that turns a limited shell plus a wordlist into a working password.

## What it's for

sucrack brute-forces a local account's password through the real `su` command on the host you are already on — the classic low-privilege-shell situation where there is no network service to retest against. A plain shell loop fails here because many su implementations insist on a pseudo-terminal and impose a several-second delay after each failed attempt; sucrack is written in C, attaches the required pty per attempt, and runs multiple worker threads so one attempt's delay overlaps the next. Candidates come from a wordlist file or stdin, an optional rewriter derives case and digit mutations of each word, and root is the default target (any account via `-u`). Expect heavy CPU use and fast-growing auth logs. Nonstandard su behavior can be tuned with the `SUCRACK_SU_PATH`, `SUCRACK_AUTH_FAILURE`, and `SUCRACK_AUTH_SUCCESS` environment variables.

## Typical use cases

### Run a straight wordlist against root

The minimal invocation needs only a wordlist.

```
sucrack wordlist.txt
```

Outcome: each word is tried as root's password via su; progress statistics reprint every 3 seconds, and a successful password is reported — verify it interactively with `su -` afterwards.

### Scale threads, stats, and word mangling

Absorb the per-failure delay and widen the candidate space.

```
sucrack -a -w 20 -s 10 -u root -r -l AFLafld wordlist.txt
```

Outcome: 20 worker threads keep attempts flowing while others wait out su's failure delay, statistics redraw every 10 seconds with ANSI formatting (`-a`), and the rewriter (`-r -l AFLafld`) additionally tries each word fully upper- or lower-cased, first/last-letter case-flipped, and digit-suffixed before moving on.

### Feed candidates from a generator

Skip the static list and pipe candidates in.

```
john --stdout --incremental | sucrack -
```

Outcome: `-` makes sucrack read candidates from stdin, so John the Ripper's incremental generator drives the attack with the same reporting and threading as a file-based run.

### Attack a non-root account

Target a specific user instead of root.

```
sucrack -u user wordlist.txt
```

Outcome: attempts `su user` rather than `su root` — useful when a service or administrative account, not root, is the pivot you need.
