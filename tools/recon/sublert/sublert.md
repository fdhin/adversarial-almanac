---
name: "sublert"
categories:
  - blackarch-recon
version: "67.56d2a12"
homepage: "https://github.com/yassineaboukir/sublert"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "yassineaboukir/sublert"
github-stars: 1034
github-pushed: "2021-02-05"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sublert

> Certificate-transparency monitor that tracks organizations and alerts whenever a new subdomain gets a TLS certificate.

## What it's for

Sublert by Yassine Aboukir is a monitoring tool rather than a one-shot enumerator: you register the domains of organizations you care about, and on every scheduled run it pulls their hostnames from certificate transparency logs, compares them against what it has already seen, and reports only the new ones — pushed as a notification to a Slack workspace, optionally with DNS resolution to keep just the working subdomains. It is designed to be run from cron at a fixed interval (ideally daily) on a small server. Use it when the goal is continuous visibility of a target's emerging infrastructure; for a single exhaustive snapshot, a passive enumerator is the better fit. One-time setup (Slack webhook and configuration) is described in the project's setup guide.

## Typical use cases

### Register a domain for monitoring

Add an organization's domain to the watch list.

```
sublert -u example.com
```

Outcome: example.com is stored in the monitored-domains list; the next scheduled run will baseline its certificate-transparency hostnames.

### Review and prune the watch list

Check which domains are tracked, or remove one.

```
sublert -a
sublert -d example.com
```

Outcome: the first command prints every monitored domain; the second removes example.com so it is no longer queried on scheduled runs.

### Scheduled monitoring run with resolution

The periodic execution that produces alerts.

```
sublert -r -t 30
```

Outcome: pulls certificate-transparency data for all monitored domains with 30 worker threads, resolves newly found subdomains to filter dead names, stores the results locally, and sends any new subdomains to the configured Slack channel.

### Reset all state

Start the monitoring database over.

```
sublert -m
```

Outcome: clears stored results and configuration; the next run re-baselines every monitored domain, so expect a burst of "new" findings.

*No corpus evidence — derived from upstream documentation.*
