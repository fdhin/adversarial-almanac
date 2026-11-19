---
name: "moloch"
categories:
  - blackarch-networking
version: "0.11.3"
homepage: "https://github.com/arkime/arkime"
packaged-by: [blackarch]
blackarch-builddate: "2016-03-23"
build-system: node
license: APACHE
meta-checked: "2026-11-09"
github-repo: "arkime/arkime"
github-stars: 7569
github-pushed: "2026-11-09"
github-archived: false
github-issues: 39
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 771
gh-activity-checked: "2026-11-09"
---

# moloch

> Moloch — large-scale full-packet capture and indexing: a C capture daemon writing PCAP plus an Elasticsearch index, with a node.js viewer for search and session drill-down.

## What it's for

Moloch is a passive network-traffic recorder for high-volume links: a single-threaded C `capture` process per interface writes every packet to rotating PCAP files on disk while simultaneously indexing session metadata into Elasticsearch, and a separate `viewer` node.js application serves a web interface where analysts search sessions by query-string expressions and pull the raw packets for any result. It is explicitly not an IDS — there is no alerting; the value is after-the-fact retrospection across terabytes of traffic that would otherwise have been discarded. The packaged 0.11.3 release is the last of the AOL "Moloch" line; the project was later renamed Arkime (the upstream repository moved from aol/moloch to arkime/arkime, and later releases renamed the binaries `arkime-capture`/`arkime-viewer`). It requires an Elasticsearch cluster and a configured `config.ini` before anything starts.

## Typical use cases

### Capture and index traffic on an interface

```
moloch-capture -c config.ini
```

Outcome: starts the capture process using the node/interface settings in `config.ini` (`interface=`, `pcapDir=`, `elasticsearch=`, optional `bpf=` filter); packets are written as rotating PCAP files under the configured pcapDir and every session is indexed into Elasticsearch, tagged with the node name.

### Serve the search interface

```
node viewer.js -c config.ini
```

Outcome: starts the viewer web application on port 8005 (configurable in `config.ini`); analysts can search across indexed sessions, inspect protocol metadata, and download the underlying PCAP segments for any session.

### Create the first viewer account

```
cd viewer && node addUser.js admin "Admin User" Password1 -admin
```

Outcome: writes an admin user into Elasticsearch via the configured `config.ini`; the `-admin` flag grants the ability to manage users from the web interface — also a quick smoke test that Elasticsearch connectivity and the config are correct before starting capture.

*No corpus evidence — derived from upstream documentation.*
