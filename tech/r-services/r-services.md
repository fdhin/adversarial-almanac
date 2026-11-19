---
technology: "BSD r-services (rexec / rlogin / rsh / rusersd)"
kind: overview
audience: offensive-security
family: network-services
layer: "Legacy Unix remote access and enumeration (TCP 512 exec, 513 login, 514 shell; rusersd via rpcbind 111)"
status: "upstream-only — bucket-2 new-unit, zero corpus evidence by design"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/512-pentesting-rexec.md), CC BY-NC 4.0, accessed 2026-11-13"
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-rlogin.md), CC BY-NC 4.0, accessed 2026-11-13"
  - "rewritten from HackTricks (src/network-services-pentesting/1026-pentesting-rusersd.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: rewritten from HackTricks per the third-party-reference rule; wire facts verified against RFC 1282 and the GNU Inetutils manuals. Zero corpus evidence by design (new-unit wave, 2026-11-13). Router page parent-authored; per-service methodology pages in this folder carry the detail."
---

# r-services — the pre-SSH Unix trust estate

> The attack surface is not a bug: it is a 1980s trust model — *the hostname IS the authentication* — still answering on legacy Unix, appliances, and forgotten build farms.

## What it is

The BSD r-services are the remote-access family that predates SSH: `rexec` (512) runs a command after a **cleartext** username/password exchange, `rlogin`/`rsh` (513/514) log you in based on **source host + username trust** expressed in `/etc/hosts.equiv` and `~/.rhosts`, and `rusersd` (RPC program 100002, reached via rpcbind on 111) enumerates logged-in users across hosts. Every property an attacker wants — no cryptography, host-based trust, and user-writable trust files — is by design.

## Why you still meet it

Legacy Unix estates, network equipment management planes, HPC/build infrastructure, and the occasional modern appliance that shipped a BusyBox variant with rlogin enabled. On an external sweep these ports are rare; on an internal engagement they are a live trust-graph into the environment.

## The anatomy that matters

- **rexec**: credentials in cleartext on the wire — capture or brute them; the diagnostics channel answers differently for bad user vs bad password, a clean enumeration oracle.
- **rlogin/rsh**: control a trusted host (or spoof its identity where srcport/ident checks are the only guard) and inherit access. A single `+ +` in a user's `.rhosts` is a lateral-movement primitive.
- **rusersd**: cheap authenticated-user enumeration across a subnet — target selection for the two above.

## Dispatch

| Port | Service | Page |
|---|---|---|
| 512/tcp | rexec | [[r-services-rexec]] |
| 513/tcp | rlogin | [[r-services-rlogin]] |
| 514/tcp | rsh (same trust model) | [[r-services-rlogin]] |
| via rpcbind 111 | rusersd | [[r-services-rusersd]] |

## Where to go next

[[r-services-rexec]], [[r-services-rlogin]], [[r-services-rusersd]]; sibling legacy surfaces [[telnet]] and [[finger]].
