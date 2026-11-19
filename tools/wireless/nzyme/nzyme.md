---
name: "nzyme"
categories:
  - blackarch-wireless
version: "1.2.2"
homepage: "https://www.nzyme.org/download"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-14"
build-system: scripts
license: custom:SSPL
meta-checked: "2026-11-09"
github-repo: "nzymeorg/nzyme"
github-stars: 1590
github-pushed: "2026-11-06"
github-archived: false
github-issues: 167
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 500
gh-activity-checked: "2026-11-09"
---

# nzyme

> Wi-Fi monitoring and defense system that turns monitor-mode interfaces into sensors for rogue-AP detection and 802.11 surveillance.

*No corpus evidence — derived from upstream documentation.*

## What it's for

nzyme (by Lennart Koopmann, the creator of Graylog) is a Java-based WiFi defense platform: monitor-mode interfaces feed captured 802.11 frames to a central nzyme node, which builds a live inventory of access points and clients, tracks channels, signal levels, and tracked-device fingerprints, and raises alerts when something anomalous appears — rogue access points (via built-in and custom bandit trackers), new devices, or monitoring/signal loss. It never decrypts traffic; it works entirely on unencrypted frame metadata. Configuration is a HOCON file (/etc/nzyme/nzyme.conf), data lives in PostgreSQL, and the web interface plus REST API are served at the configured rest_listen_uri (https://127.0.0.1:22900/ in the shipped example). Version 2.0 is in development with alpha releases; stable releases are 1.x.

## Typical use cases

### Start the nzyme node

Bring up monitoring with the packaged configuration.

```
nzyme
```

Outcome: the wrapper launches the nzyme Java process, which loads /etc/nzyme/nzyme.conf, starts the REST API and web interface (browse to the configured rest_listen_uri, for example https://127.0.0.1:22900/), and begins recording what the configured monitor interfaces see.
