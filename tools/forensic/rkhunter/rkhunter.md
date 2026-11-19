---
name: "rkhunter"
categories:
  - forensics
version: "1.4.6-4"
homepage: "http://rkhunter.sourceforge.net/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali]
arch-builddate: "2025-07-17"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "Rootkit-Hunter/rkhunter"
github-stars: 32
github-pushed: "2024-11-12"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rkhunter

> Rootkit Hunter: scans Unix systems for rootkits, backdoors and local exploits.

## What it's for

Rootkit Hunter (RKH, by Michael Boelen, 2003–2017, hosted on SourceForge) is a
host-based compromise scanner for Unix-like systems. It compares file hashes
against a shipped database, looks for known rootkit files, strings and
suspicious kernel modules, checks for hidden files, odd permission and ownership
changes, and backdoored listening ports. It only detects and reports — it never
removes anything, so findings need manual triage before acting on them. Signature
databases are refreshed from the project mirrors with `--update`.

## Typical use cases

### Run a full system check without pausing

Execute every enabled test in one unattended run.

```
rkhunter --check --sk
```

Outcome: runs all test groups, skipping the between-groups keypress prompt
(`--sk` = `--skip-keypress`); warnings are summarized on screen and written in
detail to `/var/log/rkhunter.log`.

### Schedule a daily warning-only scan

Fit RKH into cron or a CI pipeline.

```
rkhunter --cronjob --report-warnings-only
```

Outcome: non-interactive scan (implies `--check --nocolors --skip-keypress`)
that prints only warnings to stdout, suitable for piping into mail.

### Refresh the detection databases first

Update signatures and checksums before scanning.

```
rkhunter --update
```

Outcome: downloads current mirror files so the subsequent `--check` compares
against up-to-date rootkit signatures.

### Baseline a known-clean system

Teach RKH what "clean" looks like on this host.

```
rkhunter --propupd
```

Outcome: records current file properties (hashes, permissions, ownership) into
the file-properties database; later `--check` runs flag deviations from this
snapshot. Run once right after a clean install or verified hardening — running
it on an already-compromised host bakes the compromise in.

### See which tests exist

Discover test names for enabling or disabling specific groups.

```
rkhunter --list tests
```

Outcome: prints the available test names usable with `--enable`/`--disable`.

*No corpus evidence — derived from upstream documentation.*
