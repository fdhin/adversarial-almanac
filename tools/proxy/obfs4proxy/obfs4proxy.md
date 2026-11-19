---
name: "obfs4proxy"
categories:
  - blackarch-proxy
version: "0.0.13"
homepage: "https://gitlab.com/yawning/obfs4"
packaged-by: [blackarch, aur]
blackarch-builddate: "2022-03-14"
build-system: go
license: BSD
meta-checked: "2026-11-09"
github-repo: "hrustbb2/obfs4proxy"
github-stars: 0
github-pushed: "2021-12-08"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# obfs4proxy

> Yawning Angel's pluggable transport for Tor: transforms Tor traffic into random-looking bytes (obfs4) so censors cannot recognize the protocol on the wire.

## What it's for

obfs4proxy is the reference implementation of the obfs4 censorship-resistance protocol, written in Go. It sits between the Tor daemon and the network and rewrites Tor's traffic — an ntor key exchange with Elligator-2-obfuscated keys over NaCl secretbox encryption — so a censor watching the client-bridge link sees innocuous-looking random data instead of a recognizable Tor handshake. It also still speaks obfs2/obfs3 (bridge and client) and ScrambleSuit/meek (client) for transition compatibility. It is not a proxy you point applications at: per the Tor pluggable-transport specification, the `tor` daemon spawns it as a managed helper whenever the torrc references it in a `ClientTransportPlugin` or `ServerTransportPlugin` line. Its two roles are connecting to obfs4 bridges as a Tor client, and providing an obfs4 bridge as a server operator.

## Typical use cases

### Route Tor client traffic through obfs4 bridges

Add to `torrc`:

```
UseBridges 1
Bridge obfs4 198.51.100.10:443 <fingerprint> cert=<bridge-cert> iat-mode=0
ClientTransportPlugin obfs4 exec /usr/bin/obfs4proxy
```

Outcome: tor spawns obfs4proxy as a helper process and the connection to the bridge carries obfs4-obfuscated traffic, defeating signature-based blocking of the Tor protocol (obfs4 requires tor 0.2.5.x or later).

### Provide an obfs4 bridge (server side)

Add to `torrc` on the bridge host, alongside the standard bridge configuration:

```
ExtORPort auto
ServerTransportPlugin obfs4 exec /usr/bin/obfs4proxy
ServerTransportListenAddr obfs4 0.0.0.0 443
```

Outcome: obfs4proxy accepts client connections on port 443; the ready-to-share client `Bridge` line is auto-generated in `DataDirectory/pt_state/obfs4_bridgeline.txt`, and bridge parameters land in `pt_state/obfs4_state.json` unless set via `ServerTransportOptions`.

### Debug a transport that will not come up

```
ClientTransportPlugin obfs4 exec /usr/bin/obfs4proxy --enableLogging --logLevel=DEBUG
```

Outcome: with tor restarted, the transport logs verbosely to `DataDirectory/pt_state/obfs4proxy.log`, exposing handshake and parameter errors behind a failing bridge connection (by default IPs are scrubbed from logs; `--unsafeLogging` disables that).

*No corpus evidence — derived from upstream documentation.*
