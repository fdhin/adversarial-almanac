---
name: "anew"
categories:
  - misc
version: "git (2024-01-12)"
homepage: "https://github.com/tomnomnom/anew"
origin: "github"
recipe: "upstream"
packaged-by: [aur]
build-system: go
meta-checked: "2026-11-09"
github-repo: "tomnomnom/anew"
github-stars: 1658
github-pushed: "2024-01-12"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 8
---

# anew

> Append new lines to a file while skipping duplicates — a `tee -a` that deduplicates.

*No corpus evidence — derived from upstream documentation.*

## What it's for

anew (by tomnomnom) reads lines on stdin and appends to a file only those not already present in it; the newly added lines are echoed to stdout as well, so downstream stages see exactly what was fresh. It is the standard accumulator for enumeration pipelines that run repeatedly — growing a live wordlist, URL set, or host list across passes without re-adding known entries. It is line-oriented only (no sorting, no fuzzy matching — byte-equal lines are duplicates) and does not rewrite or reorder existing file content.

## Typical use cases

### Grow a results file from a fresh run

Append only unseen lines from a new enumeration pass.

```
cat new-results.txt | anew all-results.txt
```

Outcome: lines already in `all-results.txt` are dropped, new ones are appended; the new lines are also printed to the terminal, so the command's visible output is precisely the delta.

### Split the delta off to its own file

Keep the additions around for the next pipeline stage.

```
cat new-results.txt | anew all-results.txt > added-lines.txt
```

Outcome: `all-results.txt` grows by the new lines and `added-lines.txt` contains exactly those additions — a deduplicating `tee -a` that lets later stages (diffing, tagging, notifications) process only fresh findings.

### Preview additions without touching the file

Dry-run the merge before committing it.

```
cat new-results.txt | anew -d all-results.txt
```

Outcome: prints the lines that would be appended, but the target file is left unchanged — useful for sanity-checking a noisy tool's output before letting it pollute a curated list.

### Append quietly in scripted pipelines

Suppress the stdout echo when nothing downstream needs the delta.

```
cat new-results.txt | anew -q all-results.txt
```

Outcome: the file is updated exactly as in the normal run, but nothing is printed — keeps cron jobs and long pipelines silent while still deduplicating.
