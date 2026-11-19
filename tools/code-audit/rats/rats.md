---
name: "rats"
categories:
  - blackarch-code-audit
version: "6.4ba54ce"
homepage: "https://github.com/andrew-d/rough-auditing-tool-for-security"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-02-25"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "andrew-d/rough-auditing-tool-for-security"
github-stars: 91
github-pushed: "2021-05-05"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rats

> Rough Auditing Tool for Security — a database-driven scanner that flags security-sensitive function calls in C/C++, Perl, PHP, Python and Ruby source.

*No corpus evidence — derived from upstream documentation.*

## What it's for

RATS parses source files and reports every use of a function listed in its XML vulnerability databases — dangerous C library calls behind buffer overflows, TOCTOU race condition patterns, and similar — with a severity rating and remediation advice for each. It was originally developed by Secure Software Inc.; the maintained fork (`andrew-d/rough-auditing-tool-for-security`) is what distributions package. "Rough" is the operative word in its name: it will not find every bug and it will also flag things that are not bugs, so treat its output as a checklist for manual review. The language is picked from file extensions by default and can be forced per run.

## Typical use cases

### Audit a source tree

Walk a whole project at the highest warning level.

```
rats -w 3 /path/to/source
```

Outcome: loads the default per-language vulnerability databases (announcing e.g. `Entries in c database: 334`), recurses into subdirectories, and prints each finding as a `file:line: Severity: function` line followed by a description of the risk and suggested action, ending with a summary of lines analyzed. Warning level 2 is the default; `-w 1` keeps only high severity, `-w 3` (used here) adds low-severity findings.

### Produce a machine-readable report

Get findings in a form tools can consume.

```
rats --xml -d /usr/share/rats/rats-c.xml src/
```

Outcome: emits the same findings as an XML document — `<vulnerability>` elements carrying severity, type, message and location inside a `<rats_output>` root with run statistics — suitable for diffing between scans or feeding a triage pipeline. `--html` produces an HTML report instead, and `-d` can be repeated to load additional databases.

### Force the language

Scan files whose extensions lie about their content.

```
rats -l python scripts/
```

Outcome: analyzes the targets as Python regardless of filename extension, loading the Python database — valid language names are `c`, `perl`, `php`, `python` and `ruby`. Useful for templated or non-standardly-suffixed source files.

### Map external-input entry points

See where untrusted data can enter the program.

```
rats -i parser.c
```

Outcome: after the normal vulnerability report, prints a list of the function calls that were used and that accept external input — a quick map of the code's attack surface to prioritize during manual review.
