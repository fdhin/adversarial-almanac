---
name: "tinfoleak2"
categories:
  - blackarch-recon
version: "41.c45c33e"
homepage: "http://www.vicenteaguileradiaz.com/tools/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# tinfoleak2

> Interactive console for Twitter intelligence: account data, devices, apps, places, routes, followers and conversations extracted from a user's timeline.

## What it's for

tinfoleak 2.0 (by Vicente Aguilera Diaz, @vaguileradiaz) is an OSINT/SOCMINT tool that automates extraction and structuring of Twitter intelligence. Taking a user identifier, geographic coordinates or keywords, it analyzes the timeline and presents what an analyst otherwise assembles by hand: account info and activity, source applications and devices, use frequency, hashtags, mentions, likes, word-frequency text analysis, media metadata, visited places, user routes and top locations, social networks and digital identities, geolocated and tagged users, followers/friends, lists, collections and conversations. It is an interactive terminal application (menu-driven console), not a batch CLI: you launch it, enter the target in the UI and navigate result sections. It requires your own Twitter API OAuth credentials (`CONSUMER_KEY`, `CONSUMER_SECRET`, `ACCESS_TOKEN`, `ACCESS_TOKEN_SECRET`) in `tinfoleak.conf`, and its Python 2 / Tweepy stack targets the legacy Twitter API — under the current X API regime expect it to need patched dependencies or to serve as a reference implementation. It ships in Kali, BlackArch, CAINE and Buscador. The first-generation script is kept as a separate lineage note: [[tinfoleak]].

## Typical use cases

### Configure OAuth and start an analysis session

Put your API credentials in the config, then launch the console.

```
tinfoleak2
```

Outcome: the packaged wrapper runs `tinfoleak.py` from its install directory and opens the interactive console; entering a Twitter user ID makes the tool pull the timeline and populate its analysis sections (account, devices, places, followers, conversations) for interactive review.

*No corpus evidence — derived from upstream documentation.*
