---
name: "gethsploit"
categories:
  - blackarch-scanner
version: "3.144778b"
homepage: "https://github.com/KarmaHostage/gethspoit"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "KarmaHostage/gethspoit"
github-stars: 31
github-pushed: "2016-05-25"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gethsploit

> Two flagless Python launchers that enumerate live Ethereum nodes and flag the ones exposing their JSON-RPC interface to the network.

## What it's for

Gethsploit (2016-era, by KarmaHostage) finds Ethereum peers whose RPC port is open — the classic misconfiguration where `geth` runs with `--rpcaddr` beyond localhost and no API/key restriction, exposing `eth_*`, account and in early versions even `personal_*` calls to anyone. Two entry points ship in the package: `gethsploit` starts a local `geth` node, waits for it to gather peers, probes each peer's client version and then tests whether its RPC port answers (TCP 8545 for go/rust clients, TCP 4000 for python clients); `nodesploit` skips the local node and instead pulls a node list from the public ethernodes.org peer-list API, probing each entry the same way. Hits are appended to `possible_vulnerables.txt` (gethsploit) or `possible_vulnerables2.txt` (nodesploit) for follow-up with an RPC client. It takes no command-line arguments — configuration is in the scripts. Both are Python 2 tools from the pre-proof-of-stake network, so the ethernodes.org API endpoint inside `nodesploit` is a single point of failure that may no longer answer; it does not exploit anything itself, it only locates open RPC endpoints.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Crawl peers through a local geth node

With `geth` (go-ethereum) installed and not otherwise running:

```
gethsploit
```

Outcome: the script launches a `geth` instance, waits about 60 seconds for the peer table to populate, iterates the connected peers — printing each peer's address and running client version — probes the matching RPC port, then kills `geth` and repeats. Every peer with an answering RPC port is appended to `possible_vulnerables.txt` in the working directory.

### Probe the public node list without a local node

When spinning up `geth` is not an option:

```
nodesploit
```

Outcome: fetches the current network node list from the ethernodes.org API, classifies each entry by its client string, probes TCP 8545 or 4000 accordingly, and appends reachable RPC endpoints to `possible_vulnerables2.txt`, sleeping 60 seconds between list refreshes until interrupted. Treat the output as a target list for manual RPC calls (`web3_clientVersion`, `eth_accounts`) against your own authorization scope.
