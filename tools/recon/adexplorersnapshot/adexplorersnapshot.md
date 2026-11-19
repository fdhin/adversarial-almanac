---
name: "adexplorersnapshot"
categories:
  - blackarch-recon
version: "146.2f84590"
homepage: "https://github.com/c3c/ADExplorerSnapshot.py"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "c3c/ADExplorerSnapshot.py"
github-stars: 1106
github-pushed: "2026-10-28"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 27
gh-activity-checked: "2026-11-09"
---

# adexplorersnapshot

> Converts Sysinternals AD Explorer snapshots (.dat) into BloodHound data via BOFHound, or dumps every object with all attributes to NDJSON.

## What it's for

ADExplorerSnapshot.py parses the proprietary binary snapshot files produced by Sysinternals' AD Explorer when an operator browses or snapshots a domain controller's LDAP tree. Its main purpose is offline ingestion: a snapshot taken on a Windows box (or retrieved from an engagement) can be converted on your Linux analysis machine into BOFHound-format output — the preferred path, covering users, groups, computers, trusts, OUs, GPOs and certificate templates — which BOFHound then turns into BloodHound JSON. It can also emit BloodHound JSON directly (deprecated mode, with gaps such as GPO/OU collection) or dump every object with every attribute to NDJSON for ad-hoc processing. Because snapshots only contain what the LDAP server returned, session and local-admin relationships are inherently absent. Large snapshots (4 GB+) are memory-hungry — budget roughly half the snapshot size in RAM.

## Typical use cases

### Convert a snapshot for BloodHound ingestion

Turn an AD Explorer .dat file into BOFHound output ready for BloodHound.

```
ADExplorerSnapshot.py -m BOFHound snapshot.dat
```

Outcome: parses the snapshot and writes BOFHound-format files (one per collection) to the current directory; feed them to BOFHound (`bofhound -i ./dc.server.com_1234567890_bofhound.log -o output`) to produce BloodHound JSON and import for attack-path analysis — the whole chain runs offline, with no further contact to the domain.

### Dump every object with all attributes

Get a lossless, tool-neutral extraction of the snapshot.

```
ADExplorerSnapshot.py -m Objects -o ndjson_out snapshot.dat
```

Outcome: writes NDJSON to `ndjson_out/` (created if missing), one line per object with all attributes from the snapshot — grep/jq-friendly for locating specific users, ACLs, or attributes without a graph database.

### Write outputs to a dedicated folder

Keep each conversion's products separated.

```
ADExplorerSnapshot.py -o snapshot-conversion snapshot.dat
```

Outcome: all output files are written into `snapshot-conversion/` instead of the working directory; combined with `-m` this keeps the BOFHound, BloodHound, or NDJSON products of several snapshots side by side without clobbering.

*No corpus evidence — derived from upstream documentation.*
