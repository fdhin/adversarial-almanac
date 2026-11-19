---
name: "git-dumper"
categories:
  - blackarch-scanner
version: "1.0.9.r8.g2b47da1"
homepage: "https://github.com/arthaud/git-dumper"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-09-06"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "arthaud/git-dumper"
github-stars: 2705
github-pushed: "2026-09-06"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# git-dumper

> Rebuilds a website's exposed `.git` directory into a working local repository, no directory listing required.

## What it's for

When a web server serves a `.git` directory (a deploy mistake or a missing deny rule), git-dumper
(arthaud) reassembles it into a usable clone: it fetches the standard metadata files (HEAD, index,
config, packed-refs), recovers object hashes from refs and logs, downloads every object it can
name, and finally runs `git checkout` to restore the working tree — even when directory listing is
disabled, so a plain `wget -r` of the tree would not work. It reads only what the server
volunteers: a properly blocked `.git` yields nothing. The similarly named git-dump is an
independent Node.js implementation of the same idea, not the same project.

## Typical use cases

### Dump an exposed .git directory

Recover the full repository behind a leaky `.git` path.

```
git-dumper http://target.example.com/.git ./src
```

Outcome: fetches HEAD, index, refs, logs and all reachable objects into `./src`, then checks out
the current working tree — the source files and `git log` become browsable locally. This is the
standard one-liner once an exposed `.git` has been confirmed (e.g. by fetching
`http://target.example.com/.git/HEAD`).

### Dump when only the site root is known

Point the tool at the document root when the repository sits directly in it.

```
git-dumper http://target.example.com/ ./site-src
```

Outcome: same reconstruction; the `.git` suffix on the URL is optional and the tool resolves the
metadata paths under the given base itself.

### Run it straight from a source checkout

Use the script directly after cloning the repository, without installing the package.

```
python3 git_dumper.py http://target.example.com/.git ~/src
```

Outcome: identical dump from the repo's `git_dumper.py` module — handy on hosts where you do not
want to pip-install anything.

### Handle servers that answer oddly

Cope with targets that only serve the raw files over a non-GET method, or that answer with an
error status while still returning content.

```
git-dumper -X POST --any-status http://target.example.com/.git ./src
```

Outcome: every recovery request is sent with POST, and any HTTP status is accepted as long as the
body is non-empty and not HTML, letting the dump proceed against misbehaving front-ends.

*Corpus evidence: the dominant invocation is the plain `git-dumper URL DIR` form against an
exposed `.git`, via the pip-installed entry point or `git_dumper.py` from a clone; custom-transport
flags verified against the upstream README usage block.*
