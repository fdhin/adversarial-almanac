---
name: "roadrecon"
categories:
  - blackarch-windows
version: "511.f7b0c47"
homepage: "https://github.com/dirkjanm/ROADtools"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "dirkjanm/ROADtools"
github-stars: 2756
github-pushed: "2026-10-22"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 63
gh-activity-checked: "2026-11-09"
---

# roadrecon

> Dump and explore everything in an Azure AD / Entra ID tenant into a local database with a web GUI.

## What it's for

ROADrecon is the exploration tool of the ROADtools family by Dirk-jan
Mollema (`_dirkjan`). It authenticates to Azure AD / Entra ID, uses an
automatically generated model of the tenant's internal API to dump all
available directory information asynchronously into an SQLAlchemy-backed
database (SQLite by default), and then lets you analyze that snapshot
offline — through an Angular web GUI served locally, or through plugins
that query the database and export to useful formats. It is built for
both red and blue team use: after one authenticated dump you have a
permanent offline copy of users, groups, apps, roles, conditional access
policies and more. Related tools in the same repo: the [[roadlib]]
library it is built on, [[roadtx]] for token operations and [[roadoidc]]
for fake-OIDC persistence. Requires Python 3.10+.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Authenticate and save tokens for the dump

Get a token file the gather step can reuse.

```
roadrecon auth -u user@example.com -p Password1
```

Outcome: authenticates against the Azure AD graph resource (device-code
and app-only flows are available via `--device-code` / `--as-app`) and
stores the tokens in `.roadtools_auth` in the current directory.

### Dump the tenant into a local database

Collect all directory objects for offline analysis.

```
roadrecon gather -d roadrecon.db
```

Outcome: reads `.roadtools_auth` and asynchronously pulls users, groups,
applications, service principals, roles and policies into the SQLite
file `roadrecon.db`; `--mfa` adds MFA details and `--evade` slows the
dump to evade common AAD Graph enumeration detections.

### Browse the snapshot in the web GUI

Analyze the collected data interactively.

```
roadrecon gui -d roadrecon.db
```

Outcome: serves the Angular front-end on `http://127.0.0.1:5000`, which
queries the offline database directly — open it in a browser to explore
users, apps, roles and admin paths without touching the tenant again.

### Export or analyze via a plugin

Turn the database into a report or another tool's input.

```
roadrecon plugin policies -d roadrecon.db
```

Outcome: runs the conditional-access parser over the database; other
bundled plugins include `bloodhound` (export to a custom BloodHound
version), `xlsexport` (Excel workbook) and `road2timeline`, each taking
the same `-d` database option.
