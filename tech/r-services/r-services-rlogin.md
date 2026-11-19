---
technology: "rlogin / rsh (BSD r-services trust model)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: "Legacy trusted-host remote access (TCP 513 login, TCP 514 shell; /etc/hosts.equiv and ~/.rhosts; RFC 1282)"
status: "upstream-only — bucket-2 new-unit, zero corpus evidence by design"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-rlogin.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: rewritten from HackTricks per the third-party-reference rule; trust-model warnings and hostname/DNS reliance grounded in RFC 1282 (BSD Rlogin) and the ssh.com rsh historical overview cited by the source. Zero corpus evidence by design (new-unit wave 3, 2026-11-13)."
---

# rlogin (513/tcp) and rsh (514/tcp) — trust abuse

Target model: the *other* half of the Berkeley r-services — a remote
terminal (`rlogin`, 513) and remote command execution (`rsh`, 514) whose
authentication is **host trust**, not passwords: `/etc/hosts.equiv`
(system-wide) and `~/.rhosts` (per-user) declare which host/user
combinations may enter without a password. RFC 1282's own security
considerations call this model dangerous — one compromised trusted host
exposes accounts on every server that trusts it — and the model leans on
IP/DNS assumptions that spoof readily on a local network. The practical
consequence for an operator: if you control *any* trusted host or can
usurp a trusted *name*, 513/514 are passwordless doors.

Why you still meet these: legacy Unix estates where change control froze
in the 1990s — embedded controllers and industrial hosts, vendor
appliance management planes that quietly ship inetd with r-services
enabled, backup/cluster automation built on rsh pairs decades ago, and
isolated enclaves that never ran the [[ssh]] migration to completion.
Where one r-service survives, its siblings usually do too — scan and
report 512 ([[r-services-rexec|rexec]]), 513, 514 as one surface, and expect
[[r-services-rusersd|rusersd]] nearby to hand you the usernames.

## rlogin — testing the trust

Client package on Debian-family systems: `rsh-client`.

```bash
rlogin 198.51.100.10 -l <username>
rlogin 198.51.100.10 -l root        # only when the privileged account is explicitly in scope
```

If trust configuration permits the (your-source-host, username) pair,
you land a shell with **no password prompt** — that absence *is* the
finding. Work the model, not a wordlist:

- Read the trust files on any host you control or have read access to:
  `find / -name .rhosts 2>/dev/null` and inspect `/etc/hosts.equiv`.
  Entries are effectively "credentials" granted to whichever host bears
  that name.
- **Name-control attack:** when a trust entry names a host, ask who can
  change that name's DNS records or local host-table mapping. Control
  the zone (or the NFS-shared hosts file — see below) and an untrusted
  machine wears the trusted identity. RFC 1282 notes authentication
  still runs through the rlogin service, PAM, and the remote/local
  username pairing — a DNS record alone is not proof of access, so
  verify the full chain (source address, rlogin daemon, PAM policy)
  before claiming the bypass.
- Password fallback exists (rlogin prompts when trust fails) — that is
  an online credential attack on a legacy account, with all the usual
  lockout constraints; brute-force references for the family live in
  the rexec page's tooling.

## rsh — one command, same trust

```bash
rsh 198.51.100.10 <command>
rsh 198.51.100.10 -l domain\\user <command>
rsh domain/user@198.51.100.10 <command>
rsh domain\\\\user@198.51.100.10 <command>
```

The multiple username syntaxes matter against integration layers that
pass credentials through differently; try each form before concluding an
account is refused.

## .rhosts as a post-exploitation primitive

Because `~/.rhosts` lives in the user's home directory, **any write
access to a home directory is rsh access to that account** — the classic
chain being an NFS export of home directories ([[nfs]]) with root-squash
misconfiguration: drop `+ +` (or `trustedhost user`) into a target
user's `.rhosts`, then rlogin/rsh in as them. On hosts you land on,
enumerate both trust files and treat every entry as lateral-movement
mapping: it names the next machine (and user) that will accept you
without a password.

## Findings and report shape

- Trust entries granting passwordless root or admin accounts: critical,
  chain with name-control or any trusted-host compromise.
- `.rhosts`/`hosts.equiv` discoverable and permissive (`+` wildcards):
  high, even unexploited — they document the blast radius.
- Sessions in cleartext: like all r-services traffic, rlogin/rsh
  sessions (including any password fallback) are unencrypted and
  sniffable — a capture finding on shared segments.
- Hardening line: disable 513/514 everywhere SSH exists; where the
  vendor appliance cannot drop them, firewall-allowlist the exact
  trust pairs and monitor 513/514 flows as canary traffic.
