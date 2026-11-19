---
name: "easy-creds"
categories:
  - blackarch-automation
version: "45.bf9f00c"
homepage: "https://github.com/brav0hax/easy-creds"
packaged-by: [blackarch]
blackarch-builddate: "2019-01-28"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "brav0hax/easy-creds"
github-stars: 286
github-pushed: "2014-09-19"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# easy-creds

> Menu-driven suite for on-path credential theft: ARP-spoofed interception, rogue access points, and sidejacking.

## What it's for

easy-creds is an interactive bash menu that orchestrates the classic on-path credential-harvesting toolchain: ettercap and sslstrip for ARP-poisoned HTTP interception, dsniff/urlsnarf for sniffing, freeradius-wpe with hostapd/airbase-ng/mdk3 for rogue-access-point attacks that capture enterprise Wi-Fi credentials, hamster/ferret for session sidejacking, and asleap for responding to LEAP/MSCHAPv2 challenges. Reach for it in an authorized on-path lab or engagement when you want those attacks pre-wired rather than scripting each tool yourself; it launches everything inside a `screen` session so the many concurrent processes stay manageable. It is a 2014-era project: expect dependency rot before anything runs, and modern TLS defaults defeat several of its techniques on their own.

## Typical use cases

### Launch the attack menus

Pick an attack class and let it wire up the underlying tools.

```
sudo easy-creds
```

Outcome: after a prerequisite check, presents the numbered attack menu (network interception, fake access point, and related credential-harvesting attacks); selecting one starts the underlying tools with their output inside a `screen` session named easy-creds.

### Reattach to a running attack

Get back into the screens hosting the attack processes.

```
screen -r easy-creds
```

Outcome: reattaches to the easy-creds session; `ctrl-a "` lists its windows so you can flip between the individual capture tools it started.

*No corpus evidence — derived from upstream documentation.*
