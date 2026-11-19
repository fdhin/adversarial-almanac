---
name: "jackdaw"
categories:
  - blackarch-recon
version: "417.19e38d1"
homepage: "https://github.com/skelsec/jackdaw"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "skelsec/jackdaw"
github-stars: 588
github-pushed: "2025-03-15"
github-archived: false
github-issues: 26
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jackdaw

> Active Directory enumerator (by skelsec) that gathers domain objects via LDAP/SMB/Kerberos into a SQL database, then graphs how they interact and where the attack paths are.

## What it's for

A BloodHound-style AD relationship analyzer built on an SQL store instead of Neo4j: `enum` collects users, machines, OUs, groups, ACLs, shares, sessions and Kerberoast-able service accounts over LDAP and SMB; `nest` serves a web UI and Swagger API over that database with graph views, path-finding and anomaly detection (insecure UAC flags, missing SMB signing, out-of-domain machines). It also organizes password-cracking projects — upload impacket/aiosmb DCSYNC output or LSASS dumps, poll uncracked hashes, re-upload cracks, and report on shared/weak passwords. Experimental software (the DB schema may change between versions); enumeration needs domain credentials, and full `auto` enumeration with integrated SSPI works only from Windows.

## Typical use cases

### Create the database

```
jackdaw --sql sqlite:////data/jd/test.db dbinit
```

Outcome: initializes an empty SQLite database for all later commands. On Linux the absolute path means four slashes after `sqlite:` (`sqlite:////…`); any SQLAlchemy URL works (PostgreSQL etc.) via `--sql`. Skip this only when running `auto` on Windows, which initializes automatically.

### Enumerate a domain with credentials

```
jackdaw --sql sqlite:////data/jd/test.db enum 'ldap+ntlm-password://CORP\user:Password1@198.51.100.10' 'smb+ntlm-password://CORP\user:Password1@198.51.100.10'
```

Outcome: full platform-independent enumeration — the LDAP URL pulls AD objects and their security descriptors/ACLs, the SMB URL walks shares, local groups, sessions and NTLM data per machine; both end up as graph nodes/edges and SQL tables. Windows users with a domain-joined account can instead run `jackdaw auto`, which uses integrated SSPI auth (`ldap+sspi-ntlm://…`) and needs no password.

### Enumerate LDAP only

```
jackdaw --sql sqlite:////data/jd/test.db ldap 'ldap+ntlm-password://CORP\user:Password1@198.51.100.10'
```

Outcome: runs just the LDAP phase — users, machines, OUs, groups with membership and DACL edges — when you only have directory-read access or want a quick pass before committing to the slower SMB sweep of every machine.

### Explore the graph in the web interface

```
jackdaw --sql sqlite:////data/jd/test.db nest
```

Outcome: serves the "nest" web UI on `http://127.0.0.1:5000/nest` (Swagger-documented REST API at `http://127.0.0.1:5000/ui`). In the domain view, click "Generate graph cache" once to build the edge cache (much faster with the sqlite3 CLI tool on PATH), then use the graph view to browse object interactions and attack paths; the same queries are scriptable through the API.

*No corpus evidence — derived from upstream documentation.*
