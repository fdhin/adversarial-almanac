---
name: "dvcs-ripper"
categories:
  - blackarch-scanner
version: "54.2c1bbc6"
homepage: "https://github.com/kost/dvcs-ripper"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "kost/dvcs-ripper"
github-stars: 1790
github-pushed: "2024-07-19"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dvcs-ripper

> Downloads the contents of web-exposed .git/.svn/.hg/.bzr/.cvs directories even with directory listing off, then restores the working tree with the matching VCS client.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Harvesting source code left behind when version-control metadata is deployed inside a web root. Where directory browsing is disabled, the VCS formats themselves still expose their manifest and object files to plain HTTP GETs; dvcs-ripper's Perl scripts know each layout, fetch the pieces, and then invoke the matching client to materialize a working copy — `git checkout -f` for Git, `svn revert -R .` for SVN (both the old per-directory layout and the modern single-directory `wc.db` format are detected automatically), `hg revert` and `bzr revert` likewise. Run each ripper from an empty directory: the recovered repository is written into the current working folder. It recovers exactly what the server serves — a checkout of the readable objects, with no guarantee of complete history.

## Typical use cases

### Rip an exposed Git repository

```
rip-git -v -u http://target.example.com/.git/
```

Outcome: walks the remote `.git` structure (index, objects, packs, refs), downloads it and runs `git checkout -f`, leaving the site's source tree in the current directory; `-v` narrates each step. Add `-s` when the metadata is served over HTTPS with a certificate you do not want verified.

### Rip an exposed Subversion working copy

```
rip-svn -v -u http://target.example.com/.svn/
```

Outcome: detects whether the target uses the old per-directory `.svn` layout or the modern `wc.db` format, pulls the entries database and file contents, and reverts them into a readable tree.

### Rip Mercurial or Bazaar metadata

```
rip-hg -v -u http://target.example.com/.hg/
```

Outcome: fetches the `.hg` store and runs `hg revert` per file to rebuild the working copy; `rip-bzr -v -u http://target.example.com/.bzr/` is the Bazaar equivalent with the same shape.

### Keep results for many targets separated

```
rip-git -m -o /tmp/rips -v -u http://target.example.com/.git/
```

Outcome: `-o` writes the download into `/tmp/rips`, and `-m` creates a URL-derived subdirectory (such as `http__target.example.com_.git_`) under it so successive targets never collide.

### Inspect exposed CVS metadata

```
rip-cvs -v -u http://target.example.com/CVS/
```

Outcome: this ripper does not reconstruct a CVS checkout — it fetches and displays the CVS metadata files (Root, Repository, Entries), which still disclose repository paths and file inventories worth reading.
