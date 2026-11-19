---
name: "sqlping"
categories:
  - blackarch-windows
version: "4"
homepage: "http://www.sqlsecurity.com/downloads"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-13"
build-system: windows
license: custom
meta-checked: "2026-11-09"
github-repo: "Seabreg/sqlping"
github-stars: 0
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sqlping

> SQLPing 3.0 — discovers Microsoft SQL Server instances on a network and audits their account passwords with word lists.

## What it's for

SQLPing (Chip Andrews, sqlsecurity.com) is the classic SQL Server discovery-and-audit tool. Version 3.0, the one packaged here, scans hosts or ranges for SQL Server instances — including named instances hiding behind the UDP 1434 browse listener — gathers version and configuration details, and then checks for weak account passwords using word lists, combining reconnaissance and dictionary testing in one Windows GUI. The lineage is visible on the author's download page: the original SQLPing (source still available) did discovery, SQLPing2 added brute-force options with few scanning methods, and a separate SQLPing3cl command-line edition was released alongside 3.0 — that CLI edition is a different download, not what this package ships. sqlsecurity.com survives only as an archive of the old downloads, so the tool is unmaintained, but the behaviors it targets — the UDP browser service and SQL authentication with weak passwords — still exist on legacy instances and make it a quick point-and-check on engagement networks of that vintage.

GitHub: third-party mirror of sqlsecurity.com SQLPing (Seabreg/sqlping).

*No corpus evidence — derived from upstream documentation.*
