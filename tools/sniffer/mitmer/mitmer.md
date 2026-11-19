---
name: "mitmer"
categories:
  - blackarch-sniffer
version: "22.b01c7fe"
homepage: "https://github.com/husam212/MITMer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "husam212/MITMer"
github-stars: 54
github-pushed: "2014-09-04"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mitmer

> Automated LAN man-in-the-middle tool that watches victims' HTTP/DNS activity and can hijack chosen domains with locally-hosted fake pages.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Combining a LAN man-in-the-middle attack with credential phishing in one script: it MITMs a specific host or the whole LAN, displays the attacked hosts' HTTP and DNS activity, and for domain names you list it drops their DNS queries and re-answers them so they resolve to your machine, where it converts the real site into a fake page and serves it to harvest the victim's credentials. Built on Python 2 with scapy for the traffic handling and netfilter queue (nfqueue) for packet interception. Squarely an attack tool — run it only against networks and services you are authorized to test — and dated: last upstream activity was 2014.

## Typical use cases

### Attack all LAN hosts and phish one site

```
sudo mitmer -http -dnames login.example.com
```

Outcome: all hosts on the LAN are put behind the MITM, their HTTP activity is displayed live, and DNS queries for login.example.com are dropped and redirected to the attacking machine, which serves its generated fake page to victims.

### Target a single victim and two domains

```
sudo mitmer -ip 198.51.100.10 -dns -dnames "mail.example.com intranet.example.com"
```

Outcome: only 198.51.100.10 is attacked; its DNS lookups are displayed, and queries for the two listed domains resolve to the attacker and serve fake pages, while the rest of the LAN is unaffected.
