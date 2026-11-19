---
technology: "BSD r-services (rexec / rlogin / rsh / rcp; rusersd/rstatd via rpcbind)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "upstream-only, zero corpus evidence by design. Wire facts verified against RFC 1282 (BSD Rlogin) and the BSD/inetutils rshd(8) manual; trust-file semantics against hosts.equiv(5) (man7/Linux man-pages) and Solaris rhosts(4); PAM interplay against the Linux-PAM pam_rhosts(8) manual; tooling against the Metasploit rservices modules (rapid7.com/db), the nmap rexec-brute/rlogin-brute NSE docs, and hydra's rlogin/rsh/rexec modules. Companion overview: [[r-services]]."
source: "RFC 1282; rshd(8) BSD/inetutils; hosts.equiv(5) Linux man-pages; pam_rhosts(8) Linux-PAM; Solaris rhosts(4); Metasploit auxiliary/scanner/rservices/{rexec,rlogin,rsh}_login; nmap NSE rexec-brute/rlogin-brute."
---

Companion page: [[r-services|r-services overview]] — the human introduction and port dispatch. Tool notes (nmap, metasploit, hydra, netcat, rpcinfo, tcpdump) live in the separate tools vault; this file references them by name only.

---

# r-services bug bounty methodology

**Target model:** the BSD remote-access family that predates SSH — `rexec` (512/tcp), `rlogin` (513/tcp), `rsh` (514/tcp), their `rcp` companion, and the RPC info services `rusersd`/`rstatd`/`rwalld` reached through rpcbind on 111. None of it is encrypted; the login services authenticate on **source host identity**, not a secret. The product of this methodology is a **trust graph** — who trusts whom — because on these services the misconfiguration *is* the vulnerability.

**Scope discipline:** authorized engagements only. Every probe below is single-request or low-volume. The login services answer with a shell on success, so stop at proof: one command (`id`, `hostname`), capture the transcript, do not pivot further without explicit authorization. Targets in this document are `example.com`, `*.example.com`, and `198.51.100.0/24` only.

**Out of scope:** SSH ([[ssh]]) — the migration target, not an r-service — and the sibling legacy plaintext surfaces [[telnet]] and [[finger]], which carry their own methodology pages.

## Where the money is (triage priority)

| Priority | Finding | Where it lives | Pre-auth? | Severity |
|---|---|---|---|---|
| 1 | `+` / `+ +` in `/etc/hosts.equiv` or `~/.rhosts` | any rlogind/rshd with trust files | yes (any host inherits) | **Critical** — host-compromise-by-misconfig |
| 2 | Passwordless `rsh`/`rlogin` as `root` via trust | rshd/rlogind where root `.rhosts` or equiv trusts you | yes | **Critical** |
| 3 | Trust-graph pivot (host A trusts B trusts C) | multi-host r-service estate | post-foothold | **High** — lateral movement chain |
| 4 | Cleartext credentials on the wire | rexec (512), any r-service on a tappable segment | passive | **High** |
| 5 | rexec bad-user vs bad-password oracle | rexecd (512) | yes | Low–Medium (feeds brute) |
| 6 | Source-host spoofing of a trusted peer | flat L2 segment, IP-based trust | yes | High (hard; engagement-gated) |
| 7 | `rusersd`/`rwhod` logged-in-user enumeration | rpcbind 111 → RPC info services | yes | Info–Low (target selection) |
| 8 | Service simply exposed (plaintext remote shell) | any 512/513/514 reachable | — | Low/Info (hardening) |

Phases 0–1 confirm and read the wire. Phase 2 is the semantics you must know cold. Phase 3 — trust-graph enumeration — is where the finding is. Phases 4–5 are credential attack and exploitation. Work them in order on a fresh target.

---

## Phase 0 — Fingerprint live r-services (where they still hide)

Goal: confirm the family is answering, and on *what*. On an external sweep these ports are rare; internally they are a live trust-graph.

```bash
# Direct TCP services
nmap -p 512,513,514 -sV 198.51.100.0/24
# 512/tcp open  exec     (rexecd)
# 513/tcp open  login    (rlogind)
# 514/tcp open  shell    (rshd)   <- "shell" is nmap's name for rsh, not a shell banner

# RPC info services live behind rpcbind, not on fixed ports — ask the mapper
rpcinfo -p 198.51.100.10
#   program vers proto   port  service
#    100002    3   udp    ...   rusersd   <- logged-in users
#    100001    5   udp    ...   rstatd    <- kernel/uptime stats
#    100008    1   udp    ...   rwalld
nmap -sV --script rpcinfo -p 111 198.51.100.10
```

**Where they hide in modern networks** — do not assume these are extinct:

- **Legacy Unix estates** — Solaris, AIX, HP-UX, IRIX management hosts that were never migrated; `in.rshd`/`in.rlogind` launched from `inetd`.
- **Network-equipment management planes** — old appliance OSes and OOB jump hosts (the Cisco Prime LMS root-via-rsh case is the canonical example).
- **SCADA/ICS-adjacent** — engineering workstations and historians on segregated plant networks, patched on a decade-long cycle; r-services are a common relic there.
- **HPC / build farms** — `rsh` was the historical cluster fan-out transport (pre-`pdsh`/SSH); `~/.rhosts` across nodes is the whole point of the cluster.
- **Containers with ancient bases** — a Dockerfile that `apt-get install`s `rsh-server`/`inetutils-inetd`, or a BusyBox image with `rlogind` compiled in, ships a 1980s trust model into a 2020s orchestrator.

Record the service banner (nmap `-sV`) and whether `rpcbind` is answering; both feed later phases. Note that `rsh`/`rlogin` require a **privileged client source port (512–1023)** — your scanner and clients must run as root.

---

## Phase 1 — Read the wire: auth flow and the rexec oracle

Goal: understand exactly what each service puts on the wire, so your probes are precise and your packet captures are evidence.

### 1.1 rexec (512/tcp) — cleartext credentials, and an enumeration oracle

`rexecd` authenticates with a **username + password sent in cleartext**, then runs a command. The client writes, each NUL-terminated: a secondary-port string for stderr (`0` for none), the username, the password, and the command. Success returns a single `\0` byte.

```bash
# A single authenticated command (credentials in the clear on the wire)
# interactive client prompts for the password:
rexec -l alice -p 'S3cret!' 198.51.100.10 id        # inetutils rexec client
# or drive it with metasploit for scripted testing:
msfconsole -q -x "use auxiliary/scanner/rservices/rexec_login; \
  set RHOSTS 198.51.100.10; set USERNAME alice; set PASSWORD 'S3cret!'; run; exit"
```

The diagnostic channel is an **oracle**: a bad *username* and a bad *password* often return distinguishable error text ("Login incorrect" vs. a password-specific message), letting you confirm valid accounts before brute-forcing them. Probe one known-good name (`root`), one random string, and diff the responses — the same reply-shape calibration used against [[finger]].

### 1.2 rlogin (513/tcp) and rsh (514/tcp) — host-based trust, no password on the happy path

Both authenticate with `ruserok()`, which consults `/etc/hosts.equiv` then the target user's `~/.rhosts` (Phase 2). If your source host + claimed username satisfy a trust entry, **you get a session with no password at all.**

The rsh handshake, from the BSD/inetutils `rshd(8)` manual (verified):

1. **Source-port check.** The daemon rejects any client whose source port is outside 512–1023, and drops the connection. (This is why spoofing is non-trivial and why clients need root.)
2. **Stderr side channel.** The client sends a port number; the daemon opens a *second* connection back to the client on it for stderr (source port also privileged).
3. **Command read.** The daemon reads NUL-terminated strings: local user, remote user, then the command.
4. **Host resolution.** The daemon resolves the client's source IP to a hostname and canonicalises it.
5. **`ruserok()` decision.** `/etc/hosts.equiv` is checked first (**but never for root**), then `~remoteuser/.rhosts`. A match → command runs as the remote user. No match → the daemon falls back to a password (rlogin) or rejects (rsh).

```bash
# Trust-based: no password if ruserok() trusts your host+user
rlogin -l root 198.51.100.10          # drops to root shell if root trusts you
rsh 198.51.100.10 -l root id          # one command; uid=0(root) = trust win

# rcp rides the same trust (useful to read/plant files once trusted)
rcp 198.51.100.10:/etc/hosts.equiv ./equiv.evidence
```

rlogin's handshake (RFC 1282): after connect, the client sends an empty string + NUL, then `localuser\0`, `remoteuser\0`, and `termtype/speed\0`; the server replies with a `\0` byte on acceptance. Both protocols are plaintext end to end — the session, the command, and any password fallback are all sniffable.

> Classic auth-bypass context, one line each (verify per host, do not claim by banner): the `rlogin -l -froot` argument-injection bypass on some SunOS/AIX stacks (CVE-1999-0113) and the `rlogin` `TERM` buffer overflow (CVE-1999-0046) are decades old but still present on unmodified legacy estates.

---

## Phase 2 — Trust-file semantics (`.rhosts` and `hosts.equiv`)

This is the knowledge that turns a file into a finding. Two files drive `ruserok()`:

- **`/etc/hosts.equiv`** — system-wide, admin-owned. Grants trust to **every local account except root**.
- **`~/.rhosts`** — per-user, in the user's home directory, writable by that user. Grants trust to *that* account, **including root's** `~/.rhosts` for root logins.

### 2.1 Entry grammar (host, then optional user)

Each line is a **host** entry optionally followed by a **user** entry. Each part may be a plain name, an `@netgroup`, a `+` (match-any), or a `-` (explicitly deny). The granularity is what matters:

| Entry | Meaning | Attacker reading |
|---|---|---|
| `clienthost` | that host's users may log in as the matching local user | normal, scoped trust |
| `clienthost alice` | user `alice` from `clienthost` may log in as this account | cross-user trust — note it |
| `+` | **any host** (wildcard) | **Critical** — every host on earth is trusted |
| `+ +` | **any host, any user** | **Critical** — total open door |
| `+@netgroup` | any host in the netgroup | broad; enumerate the netgroup |
| `-clienthost` | deny that host | a guard — ordering matters |
| `clienthost -baduser` | trust `clienthost` except `baduser` | scoped guard |

**Ordering is a security property.** The file is read top-down and stops at the first match, so deny (`-`) lines must precede the allow lines they protect; a `-` entry placed *after* a matching `+` never fires. A single stray `+` in column one — the classic fat-finger, or a lazy "make it work" edit — means **every host is trusted**, which is host compromise by misconfiguration. Prefer FQDNs: short hostnames widen matches, and a resolver an attacker influences turns a hostname trust into an any-host trust.

### 2.2 PAM interplay — the `+` is not always a wildcard

On PAM systems the login services run through `pam_rhosts.so`, and its behaviour diverges from the classic library in one critical way (Linux-PAM `pam_rhosts(8)`, verified): a standalone `+` is treated as the any-host **wildcard only when the keyword `promiscuous` is present** on the module's line in the service's PAM file. Without `promiscuous`, a bare `+` is handled as a *negative* entry and does **not** open the door.

```
# /etc/pam.d/rsh — DANGEROUS: re-enables the classic + wildcard
auth   required   pam_rhosts.so promiscuous
```

So a `+` you find in a trust file is a Critical *if* the daemon is the classic BSD one, and *conditionally* Critical on PAM — confirm by actually attempting the trust login rather than claiming it from the file alone. Note too that `pam_rhosts` can be configured to ignore `hosts.equiv` entirely, and that the root-exclusion for `hosts.equiv` is a convention some stacks relax. **Prove the behaviour; do not infer it.**

---

## Phase 3 — Trust-graph enumeration (the finding)

Goal: a map of directed trust edges across the estate. A `+` entry is the headline finding; the *graph* is the one that pays, because it chains hosts an external scanner sees as unrelated.

### 3.1 Harvest the trust files

Once you have any foothold (trust login, a cracked rexec credential, or source access), read the files. `rsh`/`rcp` themselves are your collection tools:

```bash
# System-wide trust
rsh 198.51.100.10 -l root 'cat /etc/hosts.equiv'

# Per-user trust — every home directory is an edge source
rsh 198.51.100.10 -l root 'for h in /home/* /root; do echo "== $h"; cat $h/.rhosts 2>/dev/null; done'

# netgroups referenced by @ entries expand the graph
rsh 198.51.100.10 -l root 'cat /etc/netgroup; ypcat -k netgroup 2>/dev/null'
```

Each `host [user]` line is a directed edge **"this account trusts that host(/user)"**. Build the graph:

- **Nodes** = hosts and accounts you have seen.
- **Edges** = trust entries (who may log in where, as whom).
- **Critical nodes** = any `+`/`+ +` entry (universally reachable), and root `.rhosts` files.
- **Chains** = A trusts B, B trusts C: a foothold on A reaches C transitively, even across subnets a port scan treats as separate.

### 3.2 Enumerate logged-in users for target selection

Before brute-forcing, let `rusersd`/`rwhod` tell you which accounts are real and active — cheap authenticated-user enumeration across a subnet, the input to Phases 3–4:

```bash
rusers -l 198.51.100.10        # username, tty, host, idle, login time (RPC 100002)
rwho                           # if rwhod (513/udp) broadcasts are observable
nmap -sU --script rusersd -p 111 198.51.100.0/24
```

Active interactive users are the accounts whose `.rhosts` are worth reading and whose passwords are worth guessing. Correlate: a user logged in *from* host B on host A is itself evidence of a live trust path to test.

---

## Phase 4 — Credential and trust attacks

### 4.1 Trust login attempts (no password needed)

The highest-value test is simply *asserting* trust. Metasploit's `rlogin_login`/`rsh_login` modules test host-based trust directly (they bind a privileged source port and try the configured username as both local and remote user):

```bash
msfconsole -q -x "use auxiliary/scanner/rservices/rsh_login; \
  set RHOSTS 198.51.100.0/24; set USERNAME root; run; exit"
# SUCCESS lines mean ruserok() trusted your host for that user — Critical, no password crossed the wire
```

A win here is the Priority-1/2 finding: a trust relationship that grants you a shell for free.

### 4.2 Password brute (rexec; rlogin fallback)

When trust fails, the services fall back to passwords. Keep volume low and authorized:

```bash
# nmap NSE — rexec and rlogin have brute scripts (rsh does not; it is trust-only)
nmap -p 512 --script rexec-brute --script-args userdb=users.txt,passdb=pass.txt 198.51.100.10
sudo nmap -p 513 --script rlogin-brute 198.51.100.10   # needs root: binds a privileged source port

# hydra covers all three protocols
hydra -L users.txt -P pass.txt rexec://198.51.100.10
hydra -L users.txt -P pass.txt rlogin://198.51.100.10
```

Seed `users.txt` from Phase 3.2 (`rusers`) and from [[finger]] if 79/tcp is open — the two legacy services feed each other. Feed the rexec oracle (1.1) first to prune invalid names.

### 4.3 Passive capture — the credentials are already in the clear

Because nothing is encrypted, a tap on an authorized segment is often the whole engagement:

```bash
# capture an r-services login and read the credentials/command verbatim
tcpdump -i eth0 -s0 -w rsvc.pcap 'tcp port 512 or tcp port 513 or tcp port 514'
# then in wireshark: Follow TCP Stream shows username, password (rexec), and the command/session
```

In a bounty program this is usually a hardening note; in an authorized internal assessment, plaintext credentials on the wire is frequently the single most important observation. Watch, don't inject — passive capture is zero-volume evidence.

### 4.4 Source-host spoofing (engagement-gated, hard)

Trust is keyed on source IP + privileged source port + resolved hostname. On a flat L2 segment where you can spoof the trusted peer's address and source a privileged port, you can inherit its trust — the classic rsh spoofing attack. It requires suppressing the real host, handling the blind TCP exchange (and the stderr back-channel), and is noisy. Attempt only under explicit authorization; usually the `.rhosts` finding (Phase 3) is cleaner evidence of the same root cause.

---

## Phase 5 — Exploitation, pivot, and evidence capture

Once trusted, prove impact minimally and move the graph forward only as authorized:

```bash
# proof of access — one command, capture verbatim
rsh 198.51.100.10 -l root 'id; hostname; cat /etc/hosts.equiv'

# the trust edge that extends the pivot — read the NEXT hop's trust, do not log into it yet
rsh 198.51.100.10 -l root 'cat /root/.rhosts'
# a line "198.51.100.20 root" means .10 (as root) is trusted BY .20 — a chain to report
```

**Exfil of `.rhosts`/`hosts.equiv` as evidence.** The trust file *is* the proof of the misconfiguration; copy the minimum needed (the offending lines), redact anything beyond the finding, and never collect user data the PoC does not require. `rcp` pulls a file cleanly for the report:

```bash
rcp 198.51.100.10:/etc/hosts.equiv ./evidence/198.51.100.10-hosts.equiv.txt
```

Clean up: remove any test `.rhosts` entry you wrote, close shells, and note in the report exactly what you touched.

---

## Reporting and evidence

Structure every finding impact-first:

1. **Title.** "Any host can obtain a root shell on `198.51.100.10` via `+` in `/root/.rhosts` (host-based trust, rshd/514)" beats "r-services enabled".
2. **Preconditions.** Network reachability to the port; a privileged source port (you had root on your testing host) — nothing exotic.
3. **PoC.** The exact `rsh`/`rlogin` command and the `id`/`hostname` output; for a trust finding, the offending file line.
4. **Evidence.** The redacted trust-file excerpt and the session transcript; for cleartext exposure, the Follow-TCP-Stream screenshot. Never exfiltrate more than the proof needs; clean up test accounts and test `.rhosts` edits.
5. **Root cause + remediation.** Name the mechanism: host-based trust via `ruserok()`, `+`/`+ +` wildcard, or plaintext transport.

**Severity rules of thumb:** `+`/`+ +` trust granting a shell — Critical; trust pivot chain — High, scaled by what the chain reaches; cleartext credentials captured — High; rexec oracle / user enumeration — Low–Medium as brute-force amplifiers; service merely exposed — Low/Info.

**Remediation (report close).** There is no hardened r-services configuration worth defending on a 1983 protocol. The fix is: **disable `rexecd`/`rlogind`/`rshd`** (remove the `inetd`/`xinetd` entries or the `rsh-server`/`inetutils-*` packages), remove every `.rhosts` and `/etc/hosts.equiv`, and migrate to [[ssh]] with key-based authentication — which replaces host-based trust with cryptographic host and user identity, encrypts the channel, and closes the cleartext and spoofing classes at once. For cluster fan-out that historically relied on `rsh`, the modern equivalent is SSH-backed tooling (`pdsh -R ssh`, Ansible). If a legacy appliance cannot be migrated, segment it off the routable network and gate access at the firewall.

---

## CTF notes

- **The reflex:** `nmap -p 512,513,514 -sV` on any box that smells legacy-Unix. `shell`/`login`/`exec` open is the tell.
- **First shot:** `rsh <host> -l root id` and `rlogin -l root <host>`. Trust boxes (Metasploitable 2 is the canonical teaching target — 512/513/514 all open) often ship `/etc/hosts.equiv` or root `.rhosts` containing `+ +`, so the login just works, passwordless, as root.
- **If trust fails:** read any `.rhosts` you can reach (via LFI, an anonymous NFS/[[nfs]] export of a home dir, or a foothold), then plant your own `+ +` or `attacker-host root` line to grant yourself trust.
- **Enumerate first:** `rusers -l <host>` and `rpcinfo -p <host>` name the live accounts and RPC info services; pair with [[finger]] (79) and [[snmp]] (161) on the same host for a fuller account list.
- **Clients need root** on your attack box — `rlogin`/`rsh` bind a privileged source port, so run as root or the handshake is rejected before auth.

---

## Appendix — tool map

- **nmap** — `-p 512,513,514 -sV` service confirmation; `rexec-brute`, `rlogin-brute` NSE (no `rsh` brute — rsh is trust-only); `rpcinfo`/`rusersd` scripts — Phases 0, 3.2, 4.2.
- **rpcinfo / rusers / rwho** — RPC info-service enumeration via rpcbind 111 — Phases 0, 3.2.
- **metasploit** — `auxiliary/scanner/rservices/{rexec_login,rlogin_login,rsh_login}` — trust and credential tests — Phase 4.
- **hydra** — `rexec://`, `rlogin://`, `rsh://` modules — password brute — Phase 4.2.
- **rsh / rlogin / rexec / rcp clients** (inetutils) — probes, trust logins, file harvest — Phases 1, 3, 5.
- **tcpdump / wireshark** — passive cleartext capture — Phase 4.3.
- **seclists** — username/password lists — Phase 4.2 input.

CVE index (all ancient, verify per host, do not claim by banner): 1999-0113 (`rlogin -froot` auth bypass, some SunOS/AIX) · 1999-0046 (`rlogin` TERM overflow) · 1999-0651 (rsh/rlogin service enabled). The modern finding is never a CVE — it is the trust graph and the cleartext.

## References

- RFC 1282, BSD Rlogin (handshake, protocol) — https://www.rfc-editor.org/rfc/rfc1282
- rshd(8), BSD/inetutils remote shell server (source-port check, stderr channel, ruserok flow) — https://man.openbsd.org/OpenBSD-5.2/rshd.8 and https://gnu.org/software/inetutils/manual/html_node/rshd-invocation.html
- hosts.equiv(5), Linux man-pages (entry grammar, `+`/`-`/netgroup, ordering, root exclusion) — https://man7.org/linux/man-pages/man5/hosts.equiv.5.html
- rhosts(4), Oracle Solaris (ruserok, hosts.equiv-not-for-root, pam interplay) — https://docs.oracle.com/cd/E86824_01/html/E54775/rhosts-4.html
- pam_rhosts(8), Linux-PAM (the `promiscuous` keyword, `+` handling) — https://man.archlinux.org/man/core/pam/pam_rhosts.8.en
- Metasploit rservices modules — https://www.rapid7.com/db/modules/auxiliary/scanner/rservices/rexec_login/ · https://www.rapid7.com/db/modules/auxiliary/scanner/rservices/rsh_login/ · https://www.rapid7.com/db/modules/auxiliary/scanner/rservices/rlogin_login/
- nmap NSE rexec-brute / rlogin-brute — https://nmap.org/nsedoc/scripts/rexec-brute.html · https://sources.debian.org/src/nmap/7.98%2Bdfsg-1/scripts/rlogin-brute.nse
- Rapid7, "Hacking like it's 1985: rooting Cisco Prime LMS" (r-services in an appliance management plane) — https://www.rapid7.com/blog/post/2013/01/16/hacking-like-its-1985-rooting-the-cisco-prime-lan-management-solution/

*Compiled 2026-11-19 from RFC 1282 (BSD Rlogin), the BSD/inetutils rshd(8) manual, hosts.equiv(5) (Linux man-pages) and Solaris rhosts(4), the Linux-PAM pam_rhosts(8) manual, the Metasploit rservices modules (rapid7.com/db), and the nmap rexec-brute/rlogin-brute NSE documentation. Model claude-opus-4-8.*
