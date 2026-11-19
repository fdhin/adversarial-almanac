---
name: "wotmate"
categories:
  - crypto
version: "0.1+git20210512-0parrot1"
homepage: "https://github.com/mricon/wotmate"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "mricon/wotmate"
github-stars: 41
github-pushed: "2026-02-25"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# wotmate

> Offline PGP web-of-trust pathfinder that works from your own keyring alone.

## What it's for

wotmate reimplements the defunct PGP pathfinder service without keyservers: everything is computed from your local keyring. A first script parses your public keyring into an SQLite signature database, then two graphing tools draw the shortest signature paths from a key you trust to a target key (`graph-paths.py`) or from a target key to every fully-trusted key in the ring (`graph-to-full.py`). A third script exports the minimal set of keys and graphs as a shareable keyring (`export-keyring.py`). It is a kernel.org-maintained toolkit (patches to tools@kernel.org) for verifying how a key connects to your trust anchor — useful when deciding whether to sign an unfamiliar key. It does not fetch or sync keys; your keyring is the entire dataset.

## Typical use cases

### Build the signature database from your keyring

```
make-sqlitedb.py
```

Outcome: runs gpg over your default keyring and writes `siginfo.db` (override with `--dbfile`, point at another Gnupg home with `--gnupghome`). This database is a prerequisite for every other wotmate command.

### Trace trust paths from your key to a target key

```
graph-paths.py --fromkey SIGNERKEYID TARGETKEYID
```

Outcome: writes `graph.png` (change with `--out`, format guessed from the extension) showing up to 4 (`--maxpaths`) shortest paths within depth 4 (`--maxdepth`); without `--fromkey` the starting point is the key with ultimate trust in your ring. `--show-trust` annotates each node with validity and trust values.

### Find paths from a target key to every fully trusted key

```
graph-to-full.py TARGETKEYID
```

Outcome: draws the shortest paths from the target key to each key you have assigned full ownertrust — the right view for project keyring infrastructures where a maintained web of trust exists.

### Export the minimal keyring covering the paths

```
export-keyring.py TARGETKEYID
```

Outcome: writes the keys needed to recreate the trust paths into `export/keys/` and matching SVG graphs into `export/graphs/` (`--outdir`, `--graph-out-format`); the result is a self-contained keyring you can hand to someone whose keyring lacks the intermediate signers.

*No corpus evidence — derived from upstream documentation.*
