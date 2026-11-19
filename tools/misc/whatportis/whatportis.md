---
name: "whatportis"
categories:
  - blackarch-misc
version: "54.59a1718"
homepage: "https://github.com/ncrocfer/whatportis"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "ncrocfer/whatportis"
github-stars: 686
github-pushed: "2023-05-23"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# whatportis

> Command-line lookup of service names, port numbers and descriptions from the official IANA registry.

## What it's for

whatportis answers "which port does redis listen on?" and "what service is
5432?" from the official IANA port registry — more complete than
/etc/services and portable across systems. It keeps a local JSON database
(~/.whatportis_db.json) created on first use and refreshable with --update,
and can emit plain tables or JSON. It is a pure lookup aid: it does not scan
or connect to anything.

## Typical use cases

### Look up the port for a service name

```
whatportis redis
```

Outcome: prints a table with the matching entry — name, port, protocol and description — from the local IANA database.

### Reverse lookup from a port number

```
whatportis 5432
```

Outcome: prints the service registered on that port (postgresql, tcp/udp, with its description).

### Search without the exact name

```
whatportis mysql --like
```

Outcome: lists every registered service whose name contains the pattern (mysql, mysql-cluster, mysql-proxy, ...) with ports and descriptions.

### Get machine-readable output

```
whatportis 5432 --json
```

Outcome: emits the same record as a JSON array instead of a table, consumable by scripts and other tools.

### Refresh the local database

```
whatportis --update
```

Outcome: after confirmation, re-syncs ~/.whatportis_db.json against the IANA registry so new assignments appear in lookups.

*No corpus evidence — derived from upstream documentation.*
