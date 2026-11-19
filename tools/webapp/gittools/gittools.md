---
name: "gittools"
categories:
  - blackarch-webapp
version: "78.8fa09eb"
homepage: "https://github.com/internetwache/GitTools"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "internetwache/GitTools"
github-stars: 4188
github-pushed: "2026-09-29"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# gittools

> Three-script pipeline — finder, dumper, extractor — for discovering and recovering exposed .git repositories.

## What it's for

GitTools (internetwache) is the classic suite for websites that expose their .git directory: Finder screens hosts for a publicly readable `.git/HEAD`, Dumper downloads as much of the repository as possible when directory listing is off, and Extractor rebuilds commits and their contents from a broken or incomplete dump. Dumper is not guaranteed to recover everything — especially when history lives in pack files — which is exactly what Extractor compensates for. On BlackArch the scripts install as `gittools-gitfinder`, `gittools-gitdumper` and `gittools-extractor`; upstream they live as `Finder/gitfinder.py`, `Dumper/gitdumper.sh` and `Extractor/extractor.sh`.

## Typical use cases

### Screen a list of hosts for exposed .git

```
gittools-gitfinder -i targets.txt -o found.txt
```

Outcome: checks each host (one per line) for a `.git/HEAD` containing `refs/heads`, prints `[*] Found: DOMAIN` lines to stdout and writes the discovered domains to the output file.

### Dump a .git directory that has no listing

```
gittools-gitdumper http://target.example.com/.git/ dumped-repo
```

Outcome: reconstructs the repository object-by-object into `dumped-repo`; afterwards `cd dumped-repo && git checkout -- .` restores the working tree (`--git-dir=otherdir` if the folder is not named .git).

### Recover commits from an incomplete dump

```
gittools-extractor dumped-repo recovered
```

Outcome: walks every commit-object in the broken repository and writes each commit's restored contents under `recovered/` (one directory per commit, not sorted by date), recovering history the raw download missed.

No corpus evidence — derived from upstream documentation.
