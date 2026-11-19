---
name: "inguma"
categories:
  - blackarch-cracker
version: "0.1.1"
homepage: "http://inguma.sourceforge.net"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "inguma/inguma"
github-stars: 6
github-pushed: "2019-07-08"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# inguma

> Interactive Python penetration-testing framework from the 2006-2008 era.

## What it's for

A free penetration testing and vulnerability discovery toolkit written entirely
in Python (author Joxean Koret), organised as an interactive console in the
spirit of an early Metasploit: `discover` modules find live hosts, `gather`
modules fingerprint services and operating systems, and further categories
cover brute forcing, fuzzing, exploitation and a disassembler, with a
particular emphasis on Oracle databases. The framework is unmaintained and
written for Python 2, so it is mainly of historical and reference interest
rather than a current testing tool.

## Typical use cases

### Start the console

Open the interactive framework shell.

```
inguma
```

Outcome: prints the banner and drops into the `inguma>` prompt; the console is
the only interface — there is no argument-driven batch mode (`-d` adds debug
output, `-h` prints the command-line help and exits).

### Explore the module library

List what the framework can do before running anything.

```
inguma
```

Outcome: at the `inguma>` prompt, `help` lists the console commands and
`show discover`, `show gather`, `show brute`, `show fuzzers` and `show rce`
enumerate the loaded modules of each category; `info <module>` prints a
module's options, affected software and notes.

### Run the guided network sweep

Let the framework chain discovery and gathering automatically.

```
inguma
```

Outcome: running `autoscan` at the `inguma>` prompt asks for a target host or
network (for example `198.51.100.0/24`) and whether to brute-force credentials,
then chains host discovery, port scanning, service/OS identification and
optional brute forcing into a rolling report; `report` writes the collected
knowledge base to disk, and `save kb` / `load kb` persist it between sessions.
