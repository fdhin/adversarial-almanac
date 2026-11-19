---
name: "cvechecker"
categories:
  - blackarch-scanner
version: "4.0"
homepage: "https://github.com/sjvermeu/cvechecker"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-11-24"
build-system: autotools
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "sjvermeu/cvechecker"
github-stars: 269
github-pushed: "2025-12-09"
github-archived: true
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# cvechecker

> Local host audit: matches installed-software versions against a locally stored CVE database and reports which CVEs your binaries map to.

*No corpus evidence — derived from upstream documentation (sjvermeu/cvechecker README; the project announces it is no longer developed or maintained).*

## What it's for

cvechecker (Sven Vermeulen) is a host-auditing tool, not a network scanner: it keeps CVE entries and version-matching rules in a local SQLite database, gathers installed-software version strings from a list of files you supply (typically every executable on the system plus `/proc/version` for the kernel), and reports every CVE those versions match. The author is explicit that the method is heuristic — a distribution that backported a fix still shows the CVE because the tool cannot see revision-release patch levels, so false positives are expected and each hit needs manual verification against the package changelog. The project is archived and no longer maintained; think of it as a per-machine CVE index builder rather than a vulnerability search engine.

## Typical use cases

### Initialize the database and load CVE data

```
cvechecker -i
pullcves pull
```

Outcome: `cvechecker -i` creates the SQLite database; the companion `pullcves pull` command downloads the CVE feed and matching rules into it. Repeat the pull periodically to refresh the data before scanning.

### Scan installed software and report matching CVEs

```
find / -type f -perm -o+x > scanlist.txt
echo /proc/version >> scanlist.txt
cvechecker -b scanlist.txt
cvechecker -r
```

Outcome: `-b` parses the scanlist (world-executable files plus the kernel banner from /proc/version) and records the installed software/version pairs it can identify; `-r` then prints every matching CVE entry per binary. Entries pointing at backported fixes are the known false-positive class — verify against the distro's package changelog before acting.
