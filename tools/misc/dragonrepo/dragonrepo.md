---
name: "dragonrepo"
categories:
  - blackarch-misc
version: "1.0.2.r0.gf444764"
homepage: "https://github.com/12hrformat/dragonrepo"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "12hrformat/dragonrepo"
github-stars: 1
github-pushed: "2026-10-28"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 51
gh-activity-checked: "2026-11-09"
---

# dragonrepo

> Records the commands you run during a security assessment and turns them into HTML, Markdown and JSON reports.

## What it's for

DRAGON Report Generator removes the copy-paste drudgery from engagement reporting. You create a project, enable a shell hook in the terminal you work from, and then simply work: every command is captured, shell noise (`cd`, `ls`, editors, pagers, system-info splashes) is filtered out, and recognized security tools (nmap, ffuf, nuclei, sqlmap, ...) are categorized automatically. Evidence files — screenshots, scan output, XML/JSON exports — dropped into the project's subdirectories are folded into the report, and sensitive strings such as API keys, tokens and passwords are redacted before anything is written. Reports land under `~/.dragonrepo/projects/<name>/reports/` as report.html, report.md and report.json, with optional PDF export. Tracking is strictly per-terminal: only the shell where you evaluated the hook is recorded. Use it only on engagements you are authorized to assess.

## Typical use cases

### Track a working session

```
dragonrepo start test-lab
eval "$(dragonrepo hook)"
nmap -sV target.example.com
dragonrepo status
```

Outcome: creates (or resumes) the project test-lab, enables command tracking in this terminal only, and after the work `status` shows the active project, start time and number of captured commands.

### Generate and open the report

```
dragonrepo generate
dragonrepo open-report
```

Outcome: writes report.html, report.md and report.json under the project's `reports/` directory — evidence files placed in the project's `screenshots/`, `scans/` and `evidence/` folders are included, with detected secrets redacted — and opens the HTML report.

### Hook a specific shell explicitly

```
eval "$(dragonrepo hook zsh)"
```

Outcome: emits the integration code for zsh (also `bash` or `fish`) when auto-detection fails; a hook for a shell other than the running one prints an error instead of silently not recording.

### Export the report to PDF

```
dragonrepo pdf
```

Outcome: converts the HTML report to PDF via wkhtmltopdf or WeasyPrint, generating the report first if it does not exist yet.

### Review activity coverage

```
dragonrepo dashboard
dragonrepo tools
```

Outcome: `dashboard` summarizes activity categories and command frequency for the project; `tools` lists the security tools DRAGON recognizes for automatic categorization.

*No corpus evidence — derived from upstream documentation.*
