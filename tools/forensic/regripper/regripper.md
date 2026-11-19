---
name: "regripper"
categories:
  - blackarch-forensic
version: "119.ec96dd4"
homepage: "https://github.com/keydet89/RegRipper3.0"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-06-01"
build-system: perl
license: MIT
meta-checked: "2026-11-09"
github-repo: "keydet89/RegRipper3.0"
github-stars: 725
github-pushed: "2026-05-27"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# regripper

> Harlan Carvey's plugin-based Windows Registry extraction tool: point it at a hive and it runs the applicable forensic plugins.

## What it's for

RegRipper (by Harlan Carvey, keydet89) is the classic Windows Registry forensic workhorse: a large battery of Perl plugins, each targeting one artifact (UserAssist, TypedURLs, Run keys, shimcache, recent documents, ...), executed against an offline hive file. The CLI (`rip.pl`, packaged as `regripper` on Linux distributions, `rip.exe` on Windows) guesses the hive type, runs all hive-specific plugins automatically, or runs a single plugin or a curated profile. Output is human-readable text on stdout; a TLN (timeline) mode emits machine-parseable lines. Caveat: it does NOT process hive transaction logs — merge those (e.g. with yarp + registryFlush.py or rla.exe) before analysis if the hive is dirty.

## Typical use cases

### Auto-run every applicable plugin against a hive

Point it at a hive and let hive-type detection pick the plugins — the fastest triage of an unknown NTUSER.DAT.

```
regripper -r ntuser.dat -a > ntuser_report.txt
```

Outcome: the hive type is detected and every matching plugin runs, each prefixed by its description banner (e.g. `(NTUSER.DAT) Gets user's Adobe app cRecentFiles values`), with findings written to stdout; pipe through `grep -i <term>` to pull one artifact family out of the report.

### Run a single plugin

Extract one specific artifact from one specific hive.

```
regripper -r system -p shimcache
```

Outcome: only the named plugin executes against the hive, printing just that artifact's parsed records — quick AppCompat/execution-triage without a full run.

### Run a profile on a hive

Use a curated plugin list (e.g. the `system` or `ntuser` profile) for repeatable, scoped processing.

```
regripper -r system -f system
```

Outcome: all plugins in the selected profile run in sequence against the hive, giving consistent output across many evidence sets.

### Timeline output

Produce TLN-formatted output to feed a master timeline.

```
regripper -r system -aT
```

Outcome: the hive-specific TLN plugins run and emit `time|source|type|value` style lines suitable for merging into a case-wide timeline file; `-s SYSTEMNAME` and `-u user` tag the lines with system and user context.

### Discover available plugins

See what artifacts the installed version can extract.

```
regripper -l -c
```

Outcome: lists all plugins with their descriptions (CSV format with `-c`) so you can pick plugin or profile names for the commands above.
