---
name: "aide"
categories:
  - blackarch-defensive
version: "0.19.4"
homepage: "https://aide.github.io/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-09-11"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "aide/aide"
github-stars: 760
github-pushed: "2026-10-22"
github-archived: false
github-issues: 25
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 19
gh-activity-checked: "2026-11-09"
---

# aide

> Advanced Intrusion Detection Environment: a file-integrity checker that detects tampering against a cryptographic baseline.

## What it's for

AIDE snapshots filesystem state — checksums, permissions, ownership, and timestamps of the files selected by rule expressions in its configuration — into a database, then re-verifies the tree and reports added, removed, and changed files. Reach for it after establishing a clean baseline of a host to catch replaced binaries, backdoors, or configuration drift; a scheduled `--check` is the classic host-based intrusion-detection loop. It protects only as well as the baseline is protected: keep the database (and config) on read-only or offline media so an intruder cannot rewrite them, since a re-baselined database hides its own tracks. It inspects the local filesystem only — it is not a network IDS and not a log analyzer.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Build the baseline database

Take the first snapshot of a freshly installed, trusted host.

```
sudo aide --init
```

Outcome: walks the tree defined by the config rules and writes the new database to the `database_out` location (commonly `/var/lib/aide/aide.db.new`); move or rename it to the `database_in` path from the config (e.g. `/var/lib/aide/aide.db`) so later checks run against it.

### Check for changes since the baseline

Verify the host still matches the snapshot.

```
sudo aide --check
```

Outcome: prints a report of added, removed, and changed files; the exit status is a bitmask — 1 for new files, 2 for removed, 4 for changed, added together when several apply (e.g. 3 = new + removed) — so a cron wrapper can alert on any nonzero code. `--check` is the default command, so a plain `aide` invocation checks too.

### Preview a baseline without writing it

See what the rules would capture before committing a database.

```
sudo aide --dry-init
```

Outcome: traverses the filesystem, matches every file against the rule tree, and reports to stdout without writing a database or report — use it while tuning rules; this mode always exits 0.

### Update the baseline after intended changes

Fold legitimate modifications into the snapshot.

```
sudo aide --update
```

Outcome: checks the tree and writes an updated database — new files recorded, unchanged entries carried over — using the input and output databases configured; promote the output database to the `database_in` path as with `--init`.

### Limit a check to one subtree

Run a fast, targeted verification of a single directory.

```
sudo aide --check --limit /etc
```

Outcome: only entries whose names match the regex are checked (and, with `--update`, updated), leaving all other entries untouched — handy right after maintenance on a known subtree.

### Validate the configuration

Catch rule errors before the scheduled check hits them.

```
aide --config-check -c /etc/aide/aide.conf
```

Outcome: stops right after reading the configuration file and reports any errors found; exits clean on a valid config — run it whenever the rules change.
