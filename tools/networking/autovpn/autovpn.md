---
name: "autovpn"
categories:
  - blackarch-networking
version: "18.28b1a87"
homepage: "https://github.com/DevelopedLogic/autovpn"
packaged-by: [blackarch]
blackarch-builddate: "2021-06-13"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "DevelopedLogic/autovpn"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 2
github-pushed: "2019-07-08"
github-archived: false
github-issues: 0
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# autovpn

> Connect to a random free VPN server in a country of your choice using VPN Gate and openvpn.

## What it's for

A small Go tool (by Adhityaa Chandrasekar) that fetches the public VPN Gate server list, picks a random server in the country you name and brings up an `openvpn` tunnel to it — a throwaway egress-IP change, not a privacy solution: the author's own README calls the tool completely insecure and unmaintained. It needs root because it drives openvpn. The original repository was deleted by its author; the packaged source is the DevelopedLogic mirror of the same code.

## Typical use cases

### Tunnel through a random VPN in a chosen country

Change your egress IP quickly.

```
sudo autovpn JP
```

Outcome: downloads the current VPN Gate server list, picks a Japanese OpenVPN server and starts openvpn against it — traffic egresses from that server until the program is interrupted. Run with no argument to default to a US server.

*No corpus evidence — derived from upstream documentation.*
