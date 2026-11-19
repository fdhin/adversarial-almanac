---
name: "patchleaks"
categories:
  - scanner
version: "0.0~git20251101.8837aac-0parrot1"
homepage: "https://github.com/hatlesswizard/PatchLeaks"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: go
meta-checked: "2026-11-09"
github-repo: "hatlesswizard/PatchLeaks"
github-stars: 33
github-pushed: "2025-11-19"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 106
gh-activity-checked: "2026-11-09"
---

# patchleaks

> Diff two code versions and explains the security fix — from CVE to patch analysis.

## What it's for

PatchLeaks is a Go web application for patch-diff analysis: give it an old and a new version of a code base — two local folders, or a tracked GitHub repository — and it highlights what the vendor changed, ranks security-relevant changes first, and produces an AI-assisted explanation of the vulnerability that was fixed, with optional correlation against CVE descriptions. It is aimed at vulnerability research: understanding a fix to assess, report or reproduce the bug. Analysis happens in its local web dashboard (Folder Analysis and GitHub Product Analysis with release monitoring); the AI back end is pluggable (OpenAI, Claude, DeepSeek or local Ollama). It does not fetch advisories or scan code standalone — its input is always the pair of versions to compare.

## Typical use cases

### Start the analysis dashboard

Bring up the local web UI.

```
patchleaks -p 8080
```

Outcome: starts the dashboard server on 127.0.0.1:8080 (a random free port if `-p` is omitted); browse there and choose "Folder Analysis" with old/new directories, or "Product Analysis" to add a GitHub repository and compare its tagged versions — results render as side-by-side diffs with per-change explanations.

### Serve the dashboard for a team

Expose the dashboard on all interfaces with parallel analysis.

```
patchleaks -host 0.0.0.0 -p 8080 -t 4
```

Outcome: the dashboard becomes reachable from other hosts and runs four concurrent AI analysis threads instead of the single default, so queued version-pair analyses complete faster.

### Self-test against real-world projects

Validate the analyzer on known open-source version pairs.

```
patchleaks -test-real-world -language php,python
```

Outcome: instead of starting the server, runs the built-in validation suite against real version comparisons of major projects (WordPress, Laravel, Django, ...) for the chosen languages, logging progress to `real_world_run.log`.

No corpus evidence — derived from upstream documentation (hatlesswizard/PatchLeaks README command-line options).
