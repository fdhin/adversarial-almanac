---
name: "bloodhound-shell"
categories:
  - scanner
version: "git (2026-11-03)"
homepage: "https://github.com/kr0no/bloodhound-shell"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "kr0no/bloodhound-shell"
github-stars: 1
github-pushed: "2026-11-03"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 67
gh-activity-checked: "2026-11-09"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 2
---

# bloodhound-shell

> Interactive terminal shell for a BloodHound CE server — AD object lookup, weakness hunting, and attack-path finding without the web UI.

## What it's for

bhshell is a third-party command-line client (independent of SpecterOps) that logs into a running BloodHound CE v9 server over its API and brings the graph analysis to a terminal: look up users, computers, groups, OUs, GPOs, and certificate templates with their weaknesses flagged; run domain-wide `hunt` checks (DCSync rights, AD CS ESC paths, kerberoastable accounts, LAPS/gMSA readers, GPO control, NTLM relay, and more) sorted by severity; find shortest attack paths to Tier Zero; and run raw or saved Cypher queries. Most commands export `--json` or `-o FILE` (CSV or a plain name list) so results feed straight into other tooling. It is a client only — the data must already be ingested; the collector and the BloodHound graph tool themselves are covered by the bloodhound and bloodhound-python notes under recon/.

## Typical use cases

### Connect and hunt interactively

```
bhshell --url http://127.0.0.1:8080
```

Outcome: opens the `[bh: shell] >` prompt against your BloodHound CE instance; `login -u admin` (password or API token prompted) followed by `hunt` prints a severity-sorted table of domain-wide findings, and `hunt dcsync` drills into a single check.

### Run checks non-interactively

```
bhshell -c "hunt --json" > hunt.json
```

Outcome: executes the same shell command once, emits machine-readable JSON, and exits — suitable for scripted reporting or piping into jq.

### Export one object's full detail

Inside the shell (commands at the `[bh: shell] >` prompt):

```
user CORP\svc_account --all -o svc_account.csv
```

Outcome: prints everything BloodHound knows about that user — delegation, group nesting, inbound/outbound ACL control, sessions, owned and Tier Zero flags — and writes a CSV copy; `computer NAME -G`, `ou NAME -t`, and `gpo 'NAME' -c` follow the same shape.

### Find attack paths from owned objects

```
path --owned -g
```

Outcome: computes shortest paths from everything marked owned to Tier Zero and renders them as a terminal graph (`-g`); SVG/PNG output variants are available for reports.

### Run a saved Cypher query

```
query run stale-admins -o stale-admins.csv
```

Outcome: executes the Cypher statement saved as `~/.bhshell/queries/stale-admins.cyp` (any `.cyp` file with a description header) and exports the results; `cypher 'MATCH ...'` runs ad-hoc queries directly.

*No corpus evidence — derived from upstream documentation.*
