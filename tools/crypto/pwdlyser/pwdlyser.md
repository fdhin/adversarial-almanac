---
name: "pwdlyser"
categories:
  - blackarch-crypto
version: "136.483b9bc"
homepage: "https://github.com/ins1gn1a/Pwdlyser-CLI"
packaged-by: [blackarch]
blackarch-builddate: "2018-08-06"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "ins1gn1a/Pwdlyser-CLI"
github-stars: 32
github-pushed: "2021-08-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pwdlyser

> Analyses cracked username:password lists for reuse, masks, keyboard patterns and policy failures, with report-ready output.

## What it's for

pwdlyser automates the review of cracked password lists after a dump or assessment. It takes a colon-delimited `username:password` list (`-p`, no headers needed) and runs the checks that matter in a report: password reuse across accounts, passwords derived from usernames, organisation-name passwords, common passwords, minimum-length violations, QWERTY keyboard patterns, character-set and date analysis, and the most common Hashcat character masks. It is aimed at consultants who need both a management-level narrative and a masked, technical per-check listing rather than raw statistics, and can flag which accounts from a supplied admin-username list were cracked. This note covers the Python CLI; the author has since released a Windows GUI edition under a separate repository, but the CLI is what gets packaged and what runs anywhere with Python.

## Typical use cases

### Run the full standard analysis

```
pwdlyser -p cracked.txt --all
```

Outcome: runs every standard check over the colon-delimited list and prints the findings to the terminal; `--all` can be combined with `-o`, `--summary` or `--admin` in the same run.

### Analyse Hashcat masks for further cracking

```
pwdlyser -p cracked.txt -m
```

Outcome: frequency analysis of the most common Hashcat character masks in the list (e.g. `?u?l?l?l?l?d?d?d?d`) — add `-mc 10` to limit output to the top 10 masks ready for a follow-up mask or rule attack.

### Produce a management summary and check admin compromise

```
pwdlyser -p cracked.txt --summary -o EXAMPLE-ORG --admin admins.txt
```

Outcome: `--summary` prints a concise report-friendly paragraph covering each check, `-o EXAMPLE-ORG` additionally flags passwords containing the organisation name, and `--admin admins.txt` (a line-separated list of privileged usernames) reports which administrator accounts were cracked.

### Output a technical per-check listing

```
pwdlyser -p cracked.txt -c -oR
```

Outcome: `-c` checks every password against the bundled common-password list and `-oR` reformats all findings as report-ready dash-prefixed lines with passwords masked except the first and last characters (e.g. `P*****rd1`), suitable for pasting into a technical report.

*No corpus evidence — derived from upstream documentation.*
