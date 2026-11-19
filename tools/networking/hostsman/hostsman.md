---
name: "hostsman"
categories:
  - networking
version: "1.1.5-0parrot1"
homepage: "https://github.com/qszhuan/hostsman"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "qszhuan/hostsman"
github-stars: 28
github-pushed: "2024-08-21"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hostsman

> Cross-platform Python CLI for adding, checking, listing, and removing mappings in the hosts file.

## What it's for

hostsman (qszhuan) is a small command-line manager for the system hosts file (path auto-detected and printed at the bottom of `hostsman -h`). It lists current mappings, checks which hostnames are already mapped, inserts `HOSTNAME[:IP]` pairs (IP defaults to 127.0.0.1; hostnames sharing an IP are grouped onto one line), and removes hostnames — deleting the whole line once the last name on it goes. Every add/remove that touches an existing entry first writes a backup of the hosts file. Runs on Python 2.6+ and 3.x. This is the CLI tool packaged by Parrot, not the unrelated Windows HostsMan GUI application of the same name.

## Typical use cases

### List and search current mappings

```
hostsman -l
hostsman -c example.com www.example.com
```

Outcome: `-l` prints the whole hosts file; `-c` prints only the mapping lines matching the given hostnames (nothing when a name is unmapped) — a quick pre-flight check before overriding a name.

### Point a hostname at an address

```
sudo hostsman -i staging.example.com:198.51.100.10
```

Outcome: inserts `198.51.100.10  staging.example.com` into the hosts file after writing a backup; omitting the `:IP` part maps the name to 127.0.0.1, and passing several arguments inserts multiple mappings in one run.

### Remove a mapping

```
sudo hostsman -r staging.example.com
```

Outcome: deletes the hostname from the hosts file (backup written first); if it was the only name on its IP line the whole line disappears, otherwise the remaining names stay grouped on the shared line.

*No corpus evidence — derived from upstream documentation.*
