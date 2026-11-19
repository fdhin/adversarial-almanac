---
technology: "Erlang Port Mapper Daemon (EPMD)"
kind: overview
audience: offensive-security
family: network-services
layer: "distributed-Erlang name registry (TCP/4369): unauthenticated node-name enumeration; distribution nodes behind it authenticate with one shared cookie (MD5 challenge/response) leading to remote shell/RCE"
language: Erlang
standards: "Erlang/OTP distribution protocol (EPMD + node handshake, OTP 23+ protocol version 6)"
homepage: "https://www.erlang.org/doc/apps/erts/erl_dist_protocol.html"
github-repo: "https://github.com/erlang/otp"
evidence: "zero corpus evidence by design — upstream-only new unit (bucket-2, owner-approved)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/4369-pentesting-erlang-port-mapper-daemon-epmd.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: zero corpus evidence by design (new-unit bucket-2; built from the upstream reference plus primary sources, not the writeup corpus). Upstream-verified 2026-11-13: Erlang/OTP distribution-protocol spec (EPMD request bytes: every *_REQ preceded by a 2-byte length field — NAMES_REQ 110, DUMP 100, KILL 107, PORT2 122, ALIVE2 120; names reply = 4-byte port + 'name X at port Y' lines; handshake = 32-bit challenge + MD5(cookie+challenge) digest, cookie never in cleartext, invalid-cookie peers dropped by net_kernel after ~1s; distribution not secure by default, TLS distribution optional; protocol v6 = OTP 23+, OTP 25 dropped v5); insinuator research (default cookie: random [A-Z] 20 chars in ~/.erlang.cookie; no brute-force lockout; epmd_bf tool; erl -remsh chain); metasploit exploit/multi/misc/erlang_cookie_rce source (COOKIE option, RPORT 25672 default = the distribution port, hand-rolled send_name + MD5 challenge + rex call os:cmd). Rewritten from HackTricks (src/network-services-pentesting/4369-pentesting-erlang-port-mapper-daemon-epmd.md), CC BY-NC 4.0, accessed 2026-11-13."
---

# Erlang Port Mapper Daemon (EPMD)

> The Erlang phone book: an unauthenticated TCP/4369 registry that hands out the listening ports of every distributed node on a host. The nodes behind it trust a single shared secret — hold the cookie and the cluster hands you a shell running as the BEAM's OS user.

## What it is

EPMD maps distributed-Erlang node names to the TCP ports where those nodes accept distribution connections — nothing more: it resolves no addresses and runs no code itself. One instance per host (auto-started with the first distributed node), default port **4369**. If you see it, the host runs distributed Erlang — in practice that means **RabbitMQ** (its distribution port defaults to 25672), **CouchDB**, ejabberd/MongooseIM-style messaging platforms, Riak, and OTP release tooling.

The attack is two-layered. EPMD's own protocol is unauthenticated enumeration: every request is `<2-byte length><request byte>`, and the `NAMES` request (`00 01 6e`) returns the EPMD port plus one `name NODE at port PORT` line per registered node — free target mapping, no interaction with the nodes. The prize sits behind it: the node handshake authenticates with a **shared cookie** (a text secret, by default a randomly generated 20-character `[A:Z]` string stored in `~/.erlang.cookie`). The exchange never transmits the cookie — each side answers a 32-bit challenge with `MD5(cookie ‖ challenge)` — but a shared secret is a shared secret: leak it once and every node trusting it is yours. `net_kernel` drops peers that compute wrong digests (after about a second), and there is **no lockout**, which makes weak or operator-set cookies brute-forceable in principle.

The operator's mental model: **epmd enumerates, the cookie authenticates, `erl -remsh` finishes**. With the cookie you start a distributed Erlang node of your own bearing the same secret, attach a remote shell to the target node, and run `os:cmd()` — command execution with the service account's privileges (RabbitMQ as `rabbitmq`, CouchDB as `couchdb`; a database process running as root turns this into root). The distribution protocol is plaintext by default (no encryption, no integrity); TLS distribution exists as an opt-in that almost nobody runs.

## The anatomy that matters on an engagement

    TCP/4369 epmd ── NAMES req (00 01 6e) ──► "name rabbit@host at port 25672"   ← free node map
        ▼
    distribution port (25672 / 11500s / per-node)
        ▼
    send_name ─► status ─► challenge (32-bit) ─► challenge_reply = MD5(cookie ‖ challenge)
        ▼                                              (cookie never on the wire)
    connected: full cluster privileges
        ▼
    erl -remsh node@host  →  os:cmd("id")   ── or ──  rpc:call(node@host, os, cmd, ["id"])

Structural properties that drive findings:

1. **EPMD is the recon primitive** — anonymous, cheap, and authoritative: it names every distributed node and port on the host. (Its `KILL` request byte (`6b`) also makes it an availability toy; don't.)
2. **One cookie, whole cluster** — the per-user `~/.erlang.cookie` file is the single point of failure; compromise any host (or any config/deployment artifact carrying it) and every cookie-sharing node falls.
3. **The wrong-cookie failure is quiet and slow** (~1s disconnect), with no counter — brute force is limited only by cookie entropy, and real deployments routinely override the random default with short human-chosen strings.

## Where you'll meet it

- **RabbitMQ brokers** exposed for management/cluster traffic — epmd plus 25672; leaked cookies (`RABBITMQ_ERLANG_COOKIE` in container env, deployment repos, CI variables) convert directly to shell on the broker.
- **CouchDB installs** — the classic local privilege-escalation route: land a low-priv shell, read the couchdb user's cookie, `rpc:call` into the DB node.
- **Messaging/telephony platforms** (ejabberd, MongooseIM, Kazoo/FreeSWITCH ecosystems) where clustered BEAM apps leak `epmd` on 4369 alongside their real listeners.
- Fleet scans: `port:4369 "at port"` on internet-wide engines still returns five-figure counts.

## Reading the framework's mind

1. Enumeration costs one packet: `NAMES` is `00 01 6e` (length-prefixed byte `n`); the reply leads with a 4-byte EPMD port then `name X at port Y` lines. A `PORT2` request (byte `7a` + node name) returns one node's port plus its distribution-protocol versions.
2. OTP version gates: distribution protocol 6 arrived with OTP 23 and OTP 25 dropped v5 — an OTP 25+ node cannot pair with pre-OTP-23 peers. Relevant when forging a peer node against an old install (the Metasploit module speaks the v5-flavoured handshake).
3. `net_adm:ping('node@host')` answering `pong` is your cookie-correctness oracle before you commit to a shell attempt.
4. The remote-shell flow is one command: `erl -cookie COOKIE -name probe@attacker -remsh node@target` — everything you type then evaluates on the target node.
5. Cookie brute force has no rate limit and no lockout; default cookies are 20 chars from `[A-Z]` (out of reach), but operator-set short strings are the realistic crack target. Details and tooling in the [[epmd-bug-bounty|methodology]].

## Where to go next

- [[epmd-bug-bounty|EPMD bug bounty methodology]] — enumeration bytes/commands, cookie acquisition, the `-remsh` and `rpc:call` flows, the Metasploit module's port semantics.
- Related units: [[couchdb]] (cookie reuse escalation), [[mqtt]]-adjacent messaging estates, [[java-rmi]] for the JVM cousin of "registry plus nodes".
