---
name: "tinfoleak"
categories:
  - blackarch-recon
version: "3.6469eb3"
homepage: "https://github.com/technoskald/tinfoleak/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "technoskald/tinfoleak"
github-stars: 101
github-pushed: "2015-03-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tinfoleak

> Original 2014-era Twitter user activity analyzer by Vicente Aguilera Diaz; superseded by tinfoleak v2 — see [[tinfoleak2]].

## What it's for

This is the first release of tinfoleak: a simple Python 2 script (requirements: Tweepy) that, given a Twitter user, extracts basic account information, the devices and applications used to tweet, geolocation coordinates for a tracking map, hashtags, mentions and topics, filterable by date range and keywords. OAuth consumer/access tokens are pasted directly into `tinfoleak.py` before running. The same author rewrote the tool from scratch as tinfoleak 2.0 with a full interactive console and far broader coverage — the rewrite is the version worth using and is packaged separately as [[tinfoleak2]]. Keep this note for lineage reference only: the v1 script targets the long-gone v1.1 Twitter API surface and is of historical interest.

*No corpus evidence — derived from upstream documentation. Pointer note — canonical coverage lives in [[tinfoleak2]].*
