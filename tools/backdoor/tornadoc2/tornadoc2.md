---
name: "tornadoc2"
categories:
  - backdoor
version: "git (2026-11-04)"
homepage: "https://github.com/kamalx06/tornadoc2"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "kamalx06/tornadoc2"
github-stars: 34
github-pushed: "2026-11-04"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 14
gh-activity-checked: "2026-11-09"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# tornadoc2

> Pull-based C2 framework with ten native transports, concurrent multi-profile handlers, and end-to-end ECDSA-signed tasking. Single Go binary agent with every property baked in at build time.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Pull-based command-and-control for authorized red-team engagements. The handler (`tornadoc2.py`, Python) listens for beacons; the implant is a single Go binary with every behavioral property — transport, keys, profile, jitter — fixed at compile time, so the running agent exposes no config file, no environment variables and no command-line arguments to inspect. Ten native transports: HTTPS, QUIC/HTTP-3, WebSocket, DNS TXT, DNS-over-HTTPS, ICMP, WireGuard, SMB-pivot, TCP-bind and TCP-reverse; tasking and results are ECDSA-signed end to end. Every C2 profile under `profiles/c2/` is served concurrently on the same port and certificate — the request path selects the profile, there is no "active profile" switch. A team server supports multiplayer with role-filtered operators, an audit log, shared loot/credential stores and a dead-man's switch that can wipe the handler tree. What it does not do: there is no runtime reconfiguration of deployed agents — changing transport or keys means building and shipping a new beacon.

## Typical use cases

### Start the handler with the HTTPS beacon listener

```
python tornadoc2.py --beacon-port 8881
```

Outcome: the handler binds `0.0.0.0:8881`, auto-discovers every profile under `profiles/c2/` and registers their paths concurrently, prints the startup banner (`[BEACON] listening on 0.0.0.0:8881` …) and drops you into the local console. Use `--beacon-mtls-ca mtls_certs/ca.pem` (or `--beacon-mtls-dir`) to require mTLS from beacons, and `--beacon-profile <name>` to narrow the handler to a single profile.

### Add covert transports next to HTTP

```
sudo python tornadoc2.py --beacon-port 8881 --dns-port 53 --dns-domain c2.example.com
```

Outcome: one DNS listener on UDP 53 serves both DNS TXT and DNS-over-HTTPS for the zone `c2.example.com` alongside the HTTP beacon port; the banner prints the DNS transport line. The same pattern adds ICMP (`--icmp`, needs CAP_NET_RAW), QUIC/HTTP-3 (`--beacon-quic-port 8885`), WebSocket (`--ws-port 8884`) and WireGuard (`--wireguard` — the handler generates its keypair on first run and prints the public half to copy into agent builds).

### Enable the team server for multiplayer

```
python tornadoc2.py --beacon-port 8881 --operator-port 12341 --operator-host 0.0.0.0
```

Outcome: remote operators can attach with the remote client (`python tornadoc2.py --client`, or `--gui` for the PySide6 console) and get a role-filtered command catalogue. Note that `--operator-port` alone already binds `0.0.0.0`; pass `--operator-host 127.0.0.1` to keep the team server loopback-only. The startup banner warns when the listener is publicly exposed.

### Compile an agent from the handler console

```
beacon-build
```

Outcome: console command of the local console (also reachable via `python3 tornadoc2.py console` in daemon mode). With no arguments it starts an interactive wizard; `beacon-build -c local-config.json` compiles from a JSON build config instead. The finished Go binary lands under `beacon_output/` with its transport and keys baked in. The remote client exposes the same operation as `build [-c <file>]`, rate-limited per operator.

### Run the handler detached

```
python tornadoc2.py --daemon --beacon-port 8881 --operator-port 12341
```

Outcome: daemonizes, writes `logs/tornadoc2.pid` and redirects output to `logs/console.log`. Reattach later with `python3 tornadoc2.py console`; Ctrl+D detaches again without stopping the daemon.
