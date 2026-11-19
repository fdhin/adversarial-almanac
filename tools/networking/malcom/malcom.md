---
name: "malcom"
categories:
  - blackarch-networking
version: "708.02e55b9"
homepage: "https://github.com/tomchop/malcom"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "tomchop/malcom"
github-stars: 1173
github-pushed: "2017-11-29"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# malcom

> Web-based malware communication analyzer that graphs a host's network traffic and cross-references it with threat-intelligence feeds.

## What it's for

Malcom (MALware COMmunications Analyzer, by tomchop) turns network traffic originating from a suspicious host or network into an interactive graph, so an analyst can see which artifacts talk to which and whether any of them are known-bad. It is designed to help detect command-and-control servers, understand peer-to-peer overlays, observe DNS fast-flux infrastructure and quickly flag malicious network artifacts. It is a passive analysis and visualization platform — it does not intercept or modify live traffic itself. It is a 2013-era Python 2 application (last upstream activity 2017): it expects MongoDB and Redis to be running, wants root for packet capture features, and its intel feeds are fetched through Celery workers.

## Typical use cases

### Run the analysis web interface

```
sudo ./malcom.py -c malcom.conf
```

Outcome: starts the Malcom web server on port 8080 (copy `malcom.conf.example` to `malcom.conf` first, and make sure `mongodb` and `redis-server` are running). From the browser you can add network artifacts, import traffic, and explore the communication graph; run `./malcom.py --help` for the full option list, including listening address and feed/Celery settings.

*No corpus evidence — derived from upstream documentation.*
