---
name: "git-dump"
categories:
  - blackarch-scanner
version: "7.4c9a2a9"
homepage: "https://github.com/bahamas10/node-git-dump"
packaged-by: [blackarch]
blackarch-builddate: "2020-08-04"
build-system: node
license: MIT
meta-checked: "2026-11-09"
github-repo: "bahamas10/node-git-dump"
github-stars: 7
github-pushed: "2024-07-19"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# git-dump

> Node.js reimplementation of the `.git`-leak dump: reconstructs a remote repository from a website without directory listing.

## What it's for

git-dump (Dave Eddy, bahamas10) solves the same problem as arthaud's git-dumper — an exposed
`.git` directory on a web server — but from Node.js, based on the GitTools `gitdumper.sh`
approach: fetch the well-known metadata files, harvest object hashes from refs and reflogs,
download every object, and leave behind a directory that behaves like a git repository. It is an
independent project, not a port of the same code: different author, different runtime, same
technique. Reach for it when a Node toolchain is what is available; the Python tool covers the
same ground. It only reconstructs what the server already exposes.

## Typical use cases

### Dump a repository without directory listing

Recover the repository behind a served `.git` path.

```
git-dump https://target.example.com/.git src
```

Outcome: prints one `> GET ...` line per recovered file (HEAD, config, index, refs, logs,
objects) and leaves `./src` looking like a git checkout — `cd src && git log` shows the recovered
history; gaps in that log mark objects the server never served.

### Tune the fetch parallelism

Speed the dump up on hosts that tolerate concurrent requests.

```
git-dump https://target.example.com/.git src 10
```

Outcome: same walk with 10 concurrent workers instead of the default 3; the optional third
argument is the worker count.

*No corpus evidence — derived from upstream documentation (bahamas10/node-git-dump README usage
block).*
