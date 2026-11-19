---
name: "mxtract"
categories:
  - blackarch-forensic
version: "90.0b34376"
homepage: "https://github.com/rek7/mXtract"
packaged-by: [blackarch]
blackarch-builddate: "2021-11-20"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "rek7/mXtract"
github-stars: 587
github-pushed: "2021-11-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mxtract

> Scans live Linux process memory with a regex database to find passwords, keys and IPs, and can dump raw memory for offline analysis.

## What it's for

mXtract (by rek7) reads the memory of running processes — heap and stack by default, or every mapped range — and matches it against a user-supplied regex database, surfacing cleartext secrets programs hold while processing them (passwords, private keys, IP addresses). It is a penetration-testing tool for machines you can already inspect: it sweeps every process or a single PID, can check whether memory ranges are writable, scan process environment files, and write raw memory dumps (with Unicode stripped) for processing by other tools. Output goes to the console or to XML/HTML files. Results depend entirely on the regexes, and every invocation needs either a regex database (`-r=`) or raw-memory write mode (`-wm`).

## Typical use cases

### Sweep all processes with a regex database

Harvest secrets matching a pattern set from every process on the system.

```
sudo mxtract -r=example_regexes.db -i
```

Outcome: scans each process's heap and stack with the regexes in the database, printing matches alongside per-process name/PID/user info (`-i`). A starter database ships as `/usr/share/doc/mxtract/example_regexes.db`.

### Target one PID and save results to a directory

Focus on a single process and keep its findings on disk.

```
sudo mxtract -p=1234 -r=example_regexes.db -wr -d=/tmp/mxtract-out/
```

Outcome: scans only PID 1234 and writes the regex results into `/tmp/mxtract-out/` (default filename `regex_results.txt`, changeable with `-f=`).

### Dump a process's raw memory

Grab memory for offline tooling instead of regex-matching inline.

```
sudo mxtract -p=1234 -wm -wi
```

Outcome: writes the process's raw memory segments to files under a `1234/` output directory, with process info prepended (`-wi`); Unicode characters are stripped so standard tools can process the dumps.

### Scan every memory range with XML output

Cast a wider net — all mapped regions plus environment files — with machine-readable results.

```
sudo mxtract -a -e -r=example_regexes.db -x -f=matches.xml
```

Outcome: scans all memory ranges (not just heap/stack) and the process environment files, and formats the regex results as XML in `matches.xml`. Note the tool's own quirk: `-r=<file>` selects the regex database while bare `-r` selects HTML report output, and `-x` selects XML.

*No corpus evidence — derived from upstream documentation.*
