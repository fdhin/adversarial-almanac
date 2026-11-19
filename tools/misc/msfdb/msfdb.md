---
name: "msfdb"
categories:
  - blackarch-misc
version: "16.09c603b"
homepage: "https://github.com/BlackArch/msfdb"
packaged-by: [blackarch]
blackarch-builddate: "2020-11-08"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BlackArch/msfdb"
github-stars: 16
github-pushed: "2020-11-08"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# msfdb

> BlackArch helper that initializes and manages the PostgreSQL database used by the Metasploit Framework.

## What it's for

Metasploit needs a live PostgreSQL database for workspaces, hosts, services, credentials and loot. This is BlackArch's packaging of the `msfdb` management script: it wraps the service-control and database plumbing behind seven subcommands — init, reinit, delete, start, stop, status, and run — so `msfconsole` starts with a working `db_connection` instead of workspace errors. Every operation accepts an optional PostgreSQL port number after the subcommand (e.g. `msfdb init 5433`) for non-default server setups. It manages only the database service; it does not update or configure metasploit itself.

## Typical use cases

### Initialize the metasploit database

One-time setup after installing metasploit.

```
msfdb init
```

Outcome: starts PostgreSQL, creates the metasploit database and user, and leaves the service running; a following `msfconsole` shows a connected database via `db_status`.

### Initialize against a non-default PostgreSQL port

Point metasploit at a PostgreSQL instance on another port.

```
msfdb init 5433
```

Outcome: performs the same initialization but configures the connection for port 5433 instead of the default 5432.

### Check database status

See whether msfconsole will have workspace storage before launching it.

```
msfdb status
```

Outcome: prints the current state of the database service and configuration; `start`/`stop` bring it up or down accordingly.

### Start the database and drop into msfconsole in one step

Skip the manual two-step launch.

```
msfdb run
```

Outcome: ensures the database service is running and then launches an interactive msfconsole attached to it.

### Reset the database

Recover from a broken schema or wipe all workspace data.

```
msfdb reinit
```

Outcome: deletes the existing metasploit database and re-creates it empty; `msfdb delete` is the more thorough variant that removes the database and stops using it entirely.

*No corpus evidence — derived from upstream documentation.*
