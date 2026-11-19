---
technology: "TURN relay (STUN/TURN, coturn)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: "WebRTC media relay (TURN on UDP/TCP 3478, TLS/DTLS 5349; STUN binding; RFC 5766/6062/6156)"
status: "upstream-only — bucket-2 new-unit, zero corpus evidence by design"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/3478-pentesting-turn.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: rewritten from HackTricks per the third-party-reference rule; load-bearing facts verified against coturn's own example turnserver.conf and project wiki (REST-API credential formula, allow-loopback-peers/no-multicast-peers/no-tcp-relay semantics, denied/allowed-peer-ip precedence), coturn advisory GHSA-6g6j-r9rf-cm7p / CVE-2020-26262 (loopback bypass via 0.0.0.0 and IPv6 forms, patched 4.5.2), and the firefart/stunner README (subcommand and flag semantics). Zero corpus evidence by design (new-unit wave 3, 2026-11-13)."
---

# TURN relay (3478) — testing and pivoting

Target model: the TURN server behind a WebRTC application (voice/video
calls, screen sharing, meetings). When two browsers cannot connect
directly, media flows through this relay — and the relay decides, per
allocation, which *peer* addresses it will forward to. That policy
decision is the entire attack surface: **TURN is an authenticated SOCKS
proxy at the TCP/UDP layer**, and a misconfigured peer policy turns any
credentialed user into a pivot into the server's own network — loopback
services, RFC1918 space, and link-local targets like the cloud metadata
service at `169.254.169.254`. An SSRF finding, but not limited to HTTP
targets. Sibling pivot surfaces: [[socks-bug-bounty|SOCKS]],
[[http-proxy]] and [[squid]] for the HTTP-layer equivalents.

Mechanics (RFC 5766; TCP relays RFC 6062): the client authenticates,
requests an allocation (a relay address), authorizes a peer
(`CreatePermission`/`ChannelBind` for UDP; a `Connect` request carrying
`XOR-PEER-ADDRESS` plus `ConnectionBind` for TCP), and the server
forwards. Local policy is supposed to answer `403 (Forbidden)` for
disallowed peers.

## Phase 1 — Get valid credentials from the application

The browser receives its TURN configuration from the web app before a
call starts, inside the `RTCPeerConnection` `iceServers` argument
(`urls`, `username`, `credential`):

```javascript
new RTCPeerConnection({
  iceServers: [{ urls: "turn:turn.example.com:3478?transport=tcp",
                 username: "1716900000:user42", credential: "base64hmac==" }]
})
```

Join or start a call through your intercepting proxy and search the HTTP
history **and WebSocket messages** for `3478`, `credential`, `username`,
`password`. If TURN runs on a nonstandard port, decode a capture as STUN
in Wireshark to learn the username, then search the proxy history for
that username. Note what the credential *is* before assuming it is
secret: in the widespread coturn "TURN REST API" scheme the username is
`timestamp:userid` and the password is
`base64(hmac-sha1(temporary-username, shared-secret))` — short-lived,
but issued to **every** participant. On a platform where anyone can
register or join anonymously, TURN authentication is not a security
boundary; it is rate limiting.

**The shared secret is the real crown.** Anything that leaks the REST
secret (client bundles where the app mints credentials server-side but
the secret also shipped into a mobile app, source disclosure, a readable
config) lets you mint valid credentials for any userid with any future
timestamp — unbounded, permanent TURN access that survives password
resets of real users.

Two coturn-deployment habits produce outright default-credential
findings: servers still running the credential pair used throughout
coturn's own documentation examples (`username1`/`password1`), and
configs carrying the `ninefingers` account straight from the commented
example block in the shipped `turnserver.conf` (both the hashed
`0xbc807...` form and the plaintext `youhavetoberealistic` variant).
Test both before anything clever.

## Phase 2 — Probe the relay policy with stunner

stunner (firefart; Go) is purpose-built for this. Verified subcommands
and flags below (`-s host:port`, `-u`, `-p`, `--tls`, `--protocol tcp|udp`):

```bash
stunner info -s turn.example.com:3478          # software string, attributes
stunner range-scan -s turn.example.com:3478 -u "$U" -p "$P" --protocol tcp
stunner range-scan -s turn.example.com:3478 -u "$U" -p "$P" --protocol udp
stunner brute-transports -s turn.example.com:3478 -u "$U" -p "$P"
```

`range-scan` reports which private and restricted ranges the server will
relay to — it is the one-command answer to "is this a pivot". Run it for
**both** transports: a gateway can forbid TCP `Connect` while allowing
UDP permissions, or vice versa. Expected refusal on a hardened server:
`403` on `Connect`/`CreatePermission` for special-purpose ranges.

## Phase 3 — Pivot through the relay

stunner's socks mode opens a local SOCKS5 listener (default
`127.0.0.1:1080`) that relays TCP through the TURN allocation; its `-x`
flag (on by default) drops requests to public IPs so you stay on
internal targets:

```bash
stunner socks -s turn.example.com:3478 -u "$U" -p "$P"
sudo proxychains nmap -sT -p 80,443,8443 -sV 10.0.0.x   # internal target pattern form
curl --socks5-hostname 127.0.0.1:1080 http://169.254.169.254/latest/meta-data/
```

Caveats: socks mode relays **TCP only**, and names are resolved locally
— use IP literals for internal targets. A relayed response (metadata
document, an internal service's error page) is the vulnerability
demonstrated; prefer one harmless request and stop. Real-world impact of
this class, from public bug-bounty disclosure: TURN relays that exposed
cloud metadata credentials, loopback-only admin services, and a whole
internal `/16` to any registered user. Also check quota behavior —
whether one user can hold many simultaneous allocations (resource
exhaustion, ASVS V17) — with a *small* number of allocations, not a flood.

## Phase 4 — Retest peer filters with alternate address encodings

When plain addresses get `403`, the filter, not the policy, is often the
bug. Verified history: coturn versions before 4.5.2 denied loopback
*ranges* but still accepted the peer address `0.0.0.0` — and, with the
default IPv6 listener, `[::1]` and `[::]` — reaching the local host
(CVE-2020-26262, GHSA-6g6j-r9rf-cm7p; the advisory's own reproduction is
a socks5 curl through stunner to `http://127.0.0.1` via `0.0.0.0`).
Later coturn advisories extended the same lesson to IPv4-mapped IPv6
forms (`[::ffff:127.0.0.1]`-style peers slipping past IPv4 deny rules).

So on any version, encode the same destination several ways and retry
both the UDP permission path and the TCP `Connect` path: `0.0.0.0`,
`[::]`, `[::1]`, IPv4-mapped IPv6 of loopback and metadata addresses,
and — where the host has NAT64 routing — embedded-IPv6 forms that an
IPv4-only deny list cannot match. A mapped peer can be *accepted* while
relayed data still fails (relay-socket binding), so distinguish
"permission granted" from "data flowed" in your report.

## Hardening checklist (what a fix should look like)

coturn specifics (option semantics verified against the shipped example
config and wiki): loopback peers are denied unless `--allow-loopback-peers`
is set (documented for testing only — flag its presence in production);
`no-multicast-peers` blocks multicast/broadcast peers; `no-tcp-relay`
disables RFC 6062 TCP relays if unneeded; and in
`denied-peer-ip`/`allowed-peer-ip` lists **allow wins over deny** — an
address present in both is allowed, so allowlist-only setups without a
deny catch-all are misconfigurations, and every `allowed-peer-ip` entry
deserves scrutiny. Deny lists should cover, at minimum, `0.0.0.0/8`,
loopback, RFC1918, link-local (metadata), the IPv4-mapped `::ffff:0:0/96`
range, and NAT64/embedded forms — the encodings of Phase 4 are exactly
the gaps a bare IPv4 list leaves open.

```text
no-multicast-peers
denied-peer-ip=0.0.0.0-0.255.255.255
denied-peer-ip=10.0.0.0-10.255.255.255
denied-peer-ip=127.0.0.0-127.255.255.255
denied-peer-ip=169.254.0.0-169.254.255.255
denied-peer-ip=172.16.0.0-172.31.255.255
denied-peer-ip=192.168.0.0-192.168.255.255
denied-peer-ip=::ffff:0.0.0.0-::ffff:255.255.255.255
denied-peer-ip=64:ff9b::-64:ff9b::ffff:ffff
denied-peer-ip=fc00::-fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff
denied-peer-ip=fe80::-febf:ffff:ffff:ffff:ffff:ffff:ffff:ffff
```

(The RFC1918/multicast constants here are the *policy content itself* —
the ranges a relay must refuse.)

## Tool appendix (prose)

stunner (info/range-scan/brute-transports/socks; the purpose-built
instrument); curl and proxychains for relay validation; Wireshark's STUN
dissector for credential harvesting from captures; Burp for the
iceServers hunt in HTTP and WebSocket traffic. coturn's own
turnutils_uclient/turnutils_peer can drive allocation behavior checks
without a browser stack.
