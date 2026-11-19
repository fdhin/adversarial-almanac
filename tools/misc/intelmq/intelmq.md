---
name: "intelmq"
categories:
  - blackarch-misc
version: "3.5.0.r36.gbbe452a20"
homepage: "https://github.com/certtools/intelmq"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: python
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "certtools/intelmq"
github-stars: 1143
github-pushed: "2026-04-28"
github-archived: false
github-issues: 262
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 30
gh-activity-checked: "2026-11-09"
---

# intelmq

> Message-queue-backed framework that lets CERTs/SOCs collect, normalize, and route security feeds through chains of small "bots".

## What it's for

IntelMQ automates incident handling: it ingests threat-intelligence feeds (log files, blacklists, feeds from other CERTs) and pushes each event through a pipeline of collector, parser, expert, and output bots connected by message queues. Events are JSON documents normalized against a shared Data Harmonization Ontology, and output bots can feed PostgreSQL, Elasticsearch, Splunk, plain files, or HTTP APIs. It is a data-processing backbone for defenders, not an attack tool — reach for it when you need reliable, resumable feed processing instead of ad-hoc scripts. Everything is managed through the `intelmqctl` command.

## Typical use cases

### Run the whole pipeline

Start every configured bot at once (the "botnet").

```
intelmqctl start
```

Outcome: prints a per-bot start status and brings all collectors, parsers, experts, and outputs online; `intelmqctl status` afterwards reports each bot's running state.

### Manage a single bot

Start, stop, restart, or reload one bot without touching the rest of the pipeline.

```
intelmqctl restart file-output
```

Outcome: the named bot is restarted and a status line is printed; use `reload` instead to re-read the bot's configuration without a restart.

### Check installation health

Validate configuration, pipeline, and permissions before relying on the botnet.

```
intelmqctl check
```

Outcome: prints a checklist of INFO/WARNING/ERROR lines covering runtime, pipeline, and configuration; fix anything reported as ERROR first.

### Inspect queues and bots

See which bots exist and where events are piling up.

```
intelmqctl list queues
```

Outcome: lists every source/destination queue with its current message count — a growing source queue points at a stuck downstream bot. `intelmqctl list bots` enumerates the configured bots instead.

### Debug one bot

Run a single processing step in the foreground and read the bot's logs.

```
intelmqctl run file-output process
```

Outcome: executes the bot's `process()` method once and shows the messages it handles; `intelmqctl log file-output` prints the bot's recent log lines.

*No corpus evidence — derived from upstream documentation.*
