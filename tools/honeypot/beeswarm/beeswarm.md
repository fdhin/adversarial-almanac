---
name: "beeswarm"
categories:
  - blackarch-honeypot
version: "1183.db51ea0"
homepage: "https://github.com/honeynet/beeswarm/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "honeynet/beeswarm"
github-stars: 237
github-pushed: "2019-03-13"
github-archived: true
github-issues: 37
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# beeswarm

> Honeypot orchestration framework that deploys decoy services plus honeytoken-wielding fake clients and turns deviations from expected traffic into indicators of compromise.

## What it's for

Beeswarm is a Honeynet Project framework (lead developer Johnny Vestergaard) for easy configuration, deployment and management of honeypots. A Beeswarm deployment consists of a central server with a web UI, drones that run fake services, and clients that act as ordinary end users. Because the traffic of those clients is known in advance, anything out of the ordinary — a certificate mismatch, unexpected reuse of seeded honeytoken credentials — surfaces as an indicator of compromise instead of drowning in generic alerts. The project is archived and its own README declares it an out-of-date proof of concept, so treat it as a reference architecture for honeytoken-based detection rather than a production sensor.

## Typical use cases

### Start the management server

Bring up the central component that drones and clients register against.

```
beeswarm --server --logfile beeswarm.log
```

Outcome: beeswarm runs in server mode and (unless `--no_webui` is given) starts its web UI; after the first-run configuration pass, drones, clients and their honeytokens are managed from that UI, with events written to beeswarm.log.

### Attach a deployment to an existing server

Run a drone or client that pulls its configuration from the server.

```
beeswarm --config https://beeswarm.example.com
```

Outcome: the process retrieves its assigned configuration from the given server URL, runs the configured honeypot services or fake end-user clients, and reports observed sessions back to the server for IoC analysis.

*No corpus evidence — derived from upstream documentation.*
