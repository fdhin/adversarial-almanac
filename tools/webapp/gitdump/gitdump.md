---
name: "gitdump"
categories:
  - blackarch-webapp
version: "1.a8b6188"
homepage: "https://github.com/Ebryx/GitDump"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-19"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Ebryx/GitDump"
github-stars: 233
github-pushed: "2026-07-17"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gitdump

> Recovers a website's source from an exposed .git directory when directory listing is disabled.

## What it's for

GitDump (Ebryx) reconstructs a site's source when its `.git` directory is readable over HTTP but has no directory listing: it fetches the common metadata files (`.git/index`, `.git/HEAD`, `packed-refs`, ...), mines object SHA1s out of them, downloads the idx/pack files, and leaves a directory that `git checkout -- .` restores into a working tree. It is a single Python 3 script (`git-dump.py`; packaged installs wrap it as `gitdump`). Mind the near-namesakes covering the same attack with different tooling — the vault's `git-dumper` note and `goop-dump` — before choosing. It does not mine deleted history or branches for secrets (an upstream TODO).

## Typical use cases

### Dump a readable .git directory without listing

```
gitdump http://target.example.com/.git/
```

Outcome: fetches the .git metadata, discovers every reachable object from index/refs, downloads them, and writes the recovered repository under `./output` (in a source checkout the same runs as `python3 git-dump.py http://target.example.com/.git/`).

### Restore the working tree from the dumped objects

```
cd output && git checkout -- .
```

Outcome: materializes every recovered file into the working tree, giving the site's source as of the last commit for review and secret-hunting.

No corpus evidence — derived from upstream documentation.
