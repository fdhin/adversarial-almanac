---
name: "ligolo-mp"
categories:
  - blackarch-networking
version: "v2.2.1.r2.g7da8994"
homepage: "https://github.com/ttpreport/ligolo-mp"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: go
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "ttpreport/ligolo-mp"
github-stars: 537
github-pushed: "2026-04-29"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 17
gh-activity-checked: "2026-11-09"
---

# ligolo-mp

> Multiplayer pivoting: a reverse-tunneled agent platform where several operators share concurrent relays through one server.

## What it's for

Ligolo-MP ("pivoting like a VPN, now with friends") is an advanced fork of ligolo-ng that turns a compromised host's outbound connection into a routed tunnel, VPN-style. An agent binary (generated per-engagement with mTLS baked in, optionally obfuscated) runs on the target and dials out; the operator side gets a TUN-based route into the target network, including its loopback, without port forwarding. What it adds over ligolo-ng is the multiplayer architecture: a central server hosts all relays, multiple operators connect with their own client and see every agent and route in a shared terminal GUI, TUN interfaces and routing are managed and restored automatically, and SOCKS/HTTP proxying (with SSPI/SPNEGO auth) is built in. Do not confuse it with its sibling ligolo-ng (nicocha30), which is single-operator and uses different binaries (`ligolo-ng`, `proxy`, `agent`); the BlackArch package installs `ligolo-mp-server` (the all-in-one server binary, upstream name `ligolo-mp`) and `ligolo-mp-client`.

## Typical use cases

### Run a single-operator pivot with the all-in-one binary

```
sudo ligolo-mp-server
```

Outcome: on first run bootstraps its certificates and configuration unsupervised, then opens the terminal UI on the credentials page; after selecting operator credentials you land on the dashboard where relays are configured and mTLS agent binaries are generated. From the dashboard, generate an agent, run it on the target host, and its network becomes reachable through the automatically created TUN interface — no manual `ip tuntap`/`ip route` steps.

### Join a shared engagement server as an operator

```
ligolo-mp-client
```

Outcome: connects to the team's ligolo-mp server using the credentials stored in the operator's home directory (each system user keeps a separate config) and opens the same dashboard: every agent joined to the server, all concurrent relays, and shared visibility of other operators' pivots. This is the multiplayer mode — run the server on attack infrastructure, point every operator's client at it.

### Route agent traffic through a corporate proxy

```
ligolo-mp-client
```

with SOCKS/HTTP proxy settings (including SSPI/SPNEGO-authenticated proxies) configured from the client, so the agent's outbound connection egresses through the environment's permitted proxy.

Outcome: the agent's dial-out traverses the proxy chain instead of direct egress, letting relays establish from networks where only proxied HTTP/SOCKS traffic is allowed.

*No corpus evidence — derived from upstream documentation.*
