---
name: "redelk"
categories:
  - automation
homepage: "https://github.com/OutflankNL/RedELK"
github-repo: "OutflankNL/RedELK"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: BSD-3-Clause
github-stars: 2689
gh-checked: "2026-11-14"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# redelk

> The red team's SIEM — an ELK stack that collects and enriches C2 and redirector logs from long-running operations, and alarms when the blue team touches your infrastructure.

## What it's for

RedELK turns the blue team's own tooling against them for the red team's benefit: a central Elastic stack ingests operational logs from multiple teamservers and traffic logs from redirectors, enriches them, and exposes everything through Kibana dashboards, prepared searches, and Jupyter notebooks. Two jobs. Operational oversight for multi-scenario, multi-teamserver, multi-member, multi-month operations: historic search over beacons, screenshots, IOCs and keystroke output from one place, with a read-only view to hand a white team. And blue-team detection: built-in alarms flag your uploaded file hashes appearing at online security providers, rogue HTTP traffic or user-agents hitting c2-named redirector backends, and arbitrary `alarm`-named backend traffic by your own logic; Greynoise and Tor-exit-node enrichment separates targeted investigation from internet background noise. Alarms are toggled and configured in a JSON config with pluggable notification methods. Ships as an installer standing up the dockerized server (Elasticsearch, Kibana, Logstash, Neo4j, Jupyter); C2 servers and redirectors feed it as clients. BSD-3-Clause, Outflank.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Pre-flight the server install

```
./install-elkserver.sh dryrun
```

Outcome: Runs only the pre-install checks and writes the resolved configuration to the .env file — the recommended first pass to review memory and port settings before anything deploys.

### Deploy the full stack

```
./install-elkserver.sh
```

Outcome: Stands up full RedELK in docker, including Neo4j and the Jupyter workbook (pass `limited` to skip them, or `fixedmemory` for 1GB heaps); login credentials for the web interface are written to redelk_passwords.cfg.

### Register a teamserver

```
mounts/redelk-config/etc/crond.d/redelk
```

Outcome: The cron file where each C2 server is declared — set its $hostname to match the $FileBeatID used during that server's client install and its operational logs start flowing into the central indices.

### Review operation traffic and beacons

```
Kibana → Dashboards → Traffic | Beacon
```

Outcome: The two stock red-team dashboards — redirector traffic breakdowns and per-beacon session views over the whole operation; the daily-driver screens on long engagements.

### Search collected operation data

```
Kibana → Discover → set time range → Open → prepared search
```

Outcome: Full search over all enriched logs with saved views for the common queries (beacon data, screenshots, IOC lists) instead of raw JSON spelunking.

### Tune blue-team detection alarms

```
mounts/redelk-config/etc/redelk/config.json
```

Outcome: The main config where each built-in alarm is enabled/disabled and given its settings — filehash lookups (VirusTotal/IBM X-Force/Hybrid Analysis API keys), rogue HTTP traffic and user-agents on `c2*` backends, and the catch-all redir-backend alarm — plus Greynoise and Tor enrichment knobs.

### Diagnose a broken pipeline

```
docker logs redelk-logstash
```

Outcome: Logstash connection and parse errors — the first place to look when dashboards stay empty; pair with `docker logs redelk-elasticsearch` for storage or mapping problems.
