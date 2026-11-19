---
technology: "rusersd (rpc.rusersd)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: "Legacy ONC RPC enumeration service (RPC program 100002, versions 2/3; dynamically ported via rpcbind/portmapper 111)"
status: "upstream-only — bucket-2 new-unit, zero corpus evidence by design"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/1026-pentesting-rusersd.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: rewritten from HackTricks per the third-party-reference rule; rusers flag semantics (-a -h -i -l -u), broadcast behavior, and RPC diagnostics verified against the OpenBSD rusers(1) manual. Zero corpus evidence by design (new-unit wave 3, 2026-11-13)."
---

# rusersd (rpc.rusersd) — session and username intelligence

Target model: the SunRPC/ONC RPC daemon that answers `rusers` queries
with `who`-style data — a username-and-session enumeration service, not
an entry point. The long listing exposes username, hostname, TTY, login
time, idle time, and sometimes the remote host a session came from:
enough to name real users, spot live operator sessions, and identify
jump boxes for later targeting. It rides the same legacy-Unix wave as
[[r-services-rexec|rexec]] and [[r-services-rlogin|rlogin/rsh]] — where those
survive, rusersd usually answers too.

**Port lore correction that saves scanning time:** scan output
associates the service with 1026 (`1026/tcp`/`1026/udp` forms), but
rusersd is **not** a fixed-port service. It registers with
rpcbind/portmapper as **RPC program 100002** (commonly versions 2 and 3,
UDP and sometimes TCP) on a dynamically assigned high port. The
discovery path is rpcbind, not a port scan — see [[rpc]] for the
portmapper surface.

## Phase 1 — Locate it behind rpcbind

```bash
rpcinfo -p 198.51.100.10 | grep -i rusersd
nmap -sV -p 111 --script rpcinfo 198.51.100.10
```

What you want from the output is the program number, versions,
transports, and the *real* port rpcbind assigned:

```text
100002  2,3  32776/udp  rusersd
100002  2,3  32780/tcp  rusersd
```

If rpcbind is filtered, the service is effectively dark to you —
enumerating high ports blind for it is wasted effort.

## Phase 2 — Query it

Install the client (`rusers` package) and:

```bash
rusers -l 198.51.100.10     # long listing — the pentest mode
rusers -al 198.51.100.10    # long + answer-even-if-empty (confirms the daemon responds)
```

Broadcast mode (no host argument) queries the local segment and waits
for late responders — a fast sweep for legacy Unix hosts on a broadcast
domain you have reached. Verified flag semantics (OpenBSD rusers(1)):
`-a` prints machines that answer even with nobody logged in; `-h` sorts
by hostname; `-i` sorts by idle time (ascending); `-l` selects the long
`who`-style listing; `-u` sorts by user count. Combinations like
`rusers -il` order sessions by activity.

Sample long listing shape:

```text
tiff       potatohead:console         Sep  2 13:03   22:03
katykat    potatohead:ttyp5           Sep  1 09:35      14
```

(username, host:display/tty, login timestamp, idle time.)

## Phase 3 — Turn output into targeting

Treat the data as session intelligence feeding the rest of the estate:

- **Username harvest** → feeds credential attacks against [[ssh]],
  [[r-services-rexec|rexec]], and every other login surface on these hosts
  (legacy estates recycle the same local accounts).
- **Low idle times** → an operator is actively working that box right
  now; schedule loud actions away from active sessions.
- **Remote-origin host field** (when present) → names jump boxes,
  admin workstations, and the naming scheme of the management range —
  prime next-hop targets and password-spray priorities.
- **Host inventory** → the broadcast sweep doubles as a legacy-host
  census for the r-services and [[nfs]]/NIS surfaces these machines
  usually also expose.

## Report shape

Information disclosure (usernames, session data, internal hostnames)
pre-authentication to anyone who can reach rpcbind — low severity as a
finding, high value as recon, and usually bundled with the wider
r-services/legacy-RPC exposure finding for the host group. Fix:
disable rpc.rusersd; if the estate cannot, firewall rpcbind (111) and
the rusersd program from untrusted ranges.
