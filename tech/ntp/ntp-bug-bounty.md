---
technology: "NTP"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "corpus-grounded (1 chain — the corpus's only real NTP attack chain: nmap 123/udp confirm -> netexec 'nxc smb dc -k -M timeroast' harvesting RID:$sntp-ms$salt$hash lines -> crack machine-account passwords; plus 18 writeups of ntpdate client sync as context, 4 banner-only sightings) + upstream-verified (NVD CVE-2013-5211 monlist/REQ_MON_GETLIST amplification, pre-4.2.7p26, in-the-wild Dec 2013; ntpq(1) mode 6 semantics — readvar/rv associd 0, peers/-p, mrulist; CISA TA14-017A NTP BAF 556.9; Timeroasting: Secura/Tom Tervoort whitepaper + hashcat mode 31300 $sntp-ms$ format). Thin on corpus, upstream-heavy by design. Companion overview: [[ntp|NTP]]."
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-ntp.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# NTP bug bounty methodology

Target model: ntpd or chronyd on UDP/123 (or a Windows DC's w32time). All probes are pre-auth UDP;
keep them scoped — this protocol is a reflection vector and your traffic looks like abuse. On AD
targets treat 123/udp as an identity-surface, not a time service. Authorization assumed; the only
state-changing step anywhere here is nothing — every phase is read/craft-only, but Timeroasting
generates one datagram per RID: rate-limit against production DCs.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Version/OS leak (readvar) | mode 6 enabled (no `restrict noquery`) | yes | daemon version, processor, OS — Info |
| Peer/topology enum (peers, lpeers) | mode 6 enabled | yes | Upstream time map, internal hosts — Info/Low |
| monlist client census | ntpd < 4.2.7p26 (CVE-2013-5211) | yes | Recent-client IP list — Low/Medium |
| Amplification exposure | internet-facing + monlist answering | yes (abuse) | ~556.9x reflector (CISA TA14-017A) — Medium/High infra |
| Timeroasting (MS-SNTP RID walk) | AD DC answering 123/udp | yes | Machine/trust-account hash oracle — High (Critical on crack) |

## Phase 0 — Confirm and fingerprint

```bash
nmap -sU -p 123 -sV 198.51.100.40
# 123/udp open  ntp   (observed form; chronyd usually says so too)
ntpq -c readvar 198.51.100.40
# system variables incl. version="4.2.6p5", processor=, system="Linux", stratum=...
```

`readvar` (alias `rv`, associd 0 = system namespace per ntpq(1)) is the banner. Record the version
string verbatim: **< 4.2.7p26 means the monlist class (CVE-2013-5211) applies**. chronyd does not
speak ntpq's mode-6 protocol on 123/udp at all — its command surface is a different protocol on a
different port (323/udp, Phase 1.5), loopback-only by default, so "no mode-6 answer" ≠ closed.
*(corrected 2026-11-13 — the previous text claimed chronyc needs local access, which the chrony
documentation contradicts: remote command access is default-off but admin-exposable; see Phase 1.5)*

## Phase 1 — Topology and client enumeration

```bash
ntpq -c peers 198.51.100.40        # tally remote refid st — who this daemon syncs to
ntpq -c lpeers 198.51.100.40       # peers AND clients (associations incl. clients)
# legacy census (old ntpd only — modern answers with mrulist instead):
ntpq -c monlist 198.51.100.40      # last ~600 source addresses the daemon heard from
```

`peers`/`lpeers` give internal host lists and the stratum chain (pivot hints: upstream time
servers, core routers). `monlist` output — remote address, last-seen, packet counts — is a
pre-auth census of the daemon's clients: internal recon gold on engagements, and on an
internet-facing host it is simultaneously your **amplification finding**: the response to a tiny
 forged-source request is huge (CISA TA14-017A: BAF 556.9 via REQ_MON_GETLIST/GETLIST_1,
 CVE-2013-5211, exploited in the wild December 2013). Evidence the exposure with a
 request/response byte count of your own unicast probe; never actually reflect at a third party.
 Modern daemons (`mrulist`, nonce-gated) are not vulnerable — a clean `mrulist` handshake is
 itself a version tell.

Legacy mode-7 companion (added 2026-11-13, rewritten from HackTricks): the
older `ntpdc` client speaks the private mode-7 protocol —
`ntpdc -c monlist`, `-c listpeers`, `-c sysinfo` — and reaches daemons that
ignore ntpq. Newer ntpd builds restrict or disable mode 7 by default, so a
mode-7 answer is itself a version tell pointing at the amplification era.

## Phase 1.5 — The chrony command surface: UDP/323 (added 2026-11-13)

chronyd's monitoring protocol is NOT on 123: `chronyc` talks to UDP/323 by
default, over a Unix socket locally and IP remotely. Facts verified against
chronyc(1):

- Two access paths: the Unix domain socket (`/var/run/chrony/chronyd.sock`,
  root/chrony only) and the network — remote command access is **off by
  default** (commands accepted only from localhost) but an administrator
  exposing it needs a non-loopback `bindcmdaddress` plus a matching
  `cmdallow`; `cmdport` changes the port, `cmdratelimit` rate-limits it.
- Even when exposed, only a fixed set of *monitoring* commands is
  network-allowed: `activity`, `manual list`, `rtcdata`, `smoothing`,
  `sourcename`, `sources`, `sourcestats`, `tracking`, `waitsync`. Further
  monitoring commands can be opened with `opencommands`. **Everything that
  changes daemon behavior stays Unix-socket-only** — sent over the network
  (even from localhost) the answer is `Not authorised`.
- What the reports still leak on an exposed box: `sources`/`tracking`
  (upstream time topology, clock quality), `clients`/`serverstats` (who
  syncs to this host, rate-limit state) — recon value comparable to ntpq's
  peers/monlist, on a port nobody scans for it.

```bash
nmap -sU -sV -p 323 198.51.100.40            # is the command port even exposed?
chronyc -n -h 198.51.100.40 tracking          # remote read if cmdallow covers you
chronyc -n -h 198.51.100.40 sources -v
```

Config-audit pointers when you land on the host: `bindcmdaddress`,
`cmdport`, `cmdallow`/`cmddeny`, `opencommands`, `cmdratelimit` in
`/etc/chrony/chrony.conf` — and remember `allow` (NTP service) is
independent of `cmdallow` (command protocol).

## Phase 2 — Timeroasting on Active Directory (observed chain)

MS-SNTP: a DC's NTP responses carry a MAC keyed by the requesting computer account's NT hash —
keyed by an RID the requester chooses, with no authentication of the requester (Secura /
Tom Tervoort, 2024). The harvest is offline-crackable `MD5(NT-hash || 48-byte NTP-response)`.

```bash
# observed form — netexec module against the DC:
nxc smb dc.example.com -k -M timeroast
# TIMEROAST 1000:$sntp-ms$f53a0ae3...$1c0111e900000000...
# RID:hash per line — every machine/trust account, one datagram each

# standalone: Secura's timeroast.py / timeroast.ps1 do the same RID walk
# crack with hashcat mode 31300 (MS SNTP), --username to keep RIDs:
hashcat -m 31300 --username hashes.txt wordlists/machine-names.txt
```

What cracks: pre-created / pre-Windows-2000 machine accounts whose password equals their name,
admin-reset "convenient" passwords, and trust accounts — the classic wordlists are
computer-name-derived (lowercased hostname, hostname$). Map cracked RIDs to names afterward via
NULL-session SMB or any authenticated lookup (Secura notes the hashes themselves carry no names).
A cracked machine account is an identity: Kerberos/NTLM as that machine, RBCD and AD-CS chains
downstream. Report as Critical when a password falls, High as exposure otherwise — the oracle
itself is one UDP packet per machine account, invisible to auth logging.

## Phase 3 — Version-history CVE lane (verify before claiming)

Beyond CVE-2013-5211, ntpd carries a known remote-CVE tail (buffer overflows in mode 7 handling
on ancient 4.2.x, crypto-NAK/zero-origin time-shifting DoS classes). Rule: only claim what the
Phase 0 version string supports, checked against NVD — the observed record has zero exploitation evidence
for these, and old-daemon monlist almost always pays before memory bugs do. On defense-facing
reports, the version leak itself (Phase 0) is the finding that justifies the upgrade push.

Verified additions to the CVE lane (2026-11-13, rewritten from HackTricks;
each checked against the primary record):

- **CVE-2023-26551 … CVE-2023-26554 (ntp 4.2.8p15 and earlier; fixed
  4.2.8p16)** — out-of-bounds writes in `libntp/mstolfp.c` (ntp.org Sec 3806,
  confirmed in the 4.2.8-series changelog). Direction matters: the records
  describe a **malicious server attacking the client-side `ntpq` process**,
  not ntpd. The operator exposure is your own tooling — never point an
  unpatched diagnostic `ntpq` at arbitrary Internet hosts; the 2023 group
  also includes **CVE-2023-26555** (`praecis_parse()` in the Palisade
  reference-clock driver, Sec 3807), which needs a manipulated
  reference-clock input path, not the network port.
- **CVE-2023-33192 (ntpd-rs before 0.3.3)** — the Rust implementation does
  not validate NTS-cookie length in received packets; one crafted packet
  **crashes the server, including deployments not configured for NTS**
  (CVE record, GitHub CNA). Port-123 DoS on a modern stack — check the
  daemon family, not just the port.
- **CVE-2025-58066 / GHSA-4855-q42w-5vr4 (ntpd-rs 1.2.0–1.6.1, server
  mode)** — a response/request validation error: the server replies even to
  *other servers' response* packets, so one spoofed packet naming server B
  can make two exposed ntpd-rs servers loop replies at each other
  indefinitely (a self-sustaining two-node storm — a different primitive
  from spoofed-source amplification). Fixed in 1.6.2; client-only configs
  unaffected. Detection shape on a monitored network: alternating UDP/123
  traffic between two servers with no client requests in sight.

## Phase 4 — NTS and the 4460/tcp key-establishment surface (added 2026-11-13)

Network Time Security (RFC 8915) moves the crypto to a separate
**TLS 1.3 channel on 4460/tcp** (`ntske/1` via ALPN) that keys authenticated
NTP packets. For recon it is a TLS endpoint like any other:

```bash
nmap -sV -p 4460 --script ssl-enum-ciphers,ssl-cert 198.51.100.40
openssl s_client -connect 198.51.100.40:4460 -alpn ntske/1 -tls1_3 -ign_eof
```

Read it like a TLS assessment: weak/legacy cipher negotiation, certificate
validation failures, unexpected trust anchors, a missing `ntske/1` ALPN
answer, and parser robustness (CVE-2023-33192 above is the worked example
of NTS-adjacent code crashing a daemon). One conceptual anchor for reports:
NTS authenticates the *source* of time — it cannot make a genuinely trusted
but malicious server truthful (see Phase 5).

## Phase 5 — Attacking time itself: malicious sources, shifting, pools (added 2026-11-13)

Amplification and hash-oracles are not the only lanes; the integrity of time
is the target on authenticated deployments too:

- **Time-shift/delay attacks.** An on-path attacker can drop or delay
  packets even against authenticated NTP; RFC 9523's **Khronos** mechanism
  (robust selection over a large diverse pool) is the standardized defense.
  chrony's `maxdistance`, `maxjitter`, and `minsources` are the practical
  source-selection safeguards to audit — configuration controls, not a
  Khronos implementation.
- **Malicious time source vs SNTP clients.** Steer an unauthenticated SNTP
  client to a server you control (config, DHCP, DNS) and it accepts shifted
  mode-4 replies — the reply must copy the request's transmit timestamp
  into its origin field (RFC 5905 mechanics), so this is a malicious-*source*
  attack, not blind spoofing. A 2025 client study (SIDN Labs) found tested
  `systemd-timesyncd`, macOS, and W32Time clients accepting malicious time
  under this prerequisite while full NTP clients rejected the tested
  offsets — check the actual client before claiming universal impact. A
  shifted clock breaks Kerberos and every time-bound control downstream
  ([[kerberos]]); in a lab, a minimal mode-4 responder or a `chronyd`
  config with `local stratum 8` + `allow` demonstrates the effect cleanly,
  then restore the clock.
- **Pool concentration.** Authentication diversity is illusory when the
  "independent" servers share an operator: a 2026 NDSS measurement of the
  NTP Pool found most active servers cluster under a small fraction of
  independent operators, and well-placed high-capacity registrations could
  capture the bulk of country-zone traffic. On assessment, resolve pool
  names repeatedly from multiple vantage points and group answers by
  operator/ASN/prefix — four addresses are not four trust domains. For
  critical clients the recommendation writes itself: pinned, independently
  administered NTS sources.

Config-audit pointers beyond chrony: `/etc/ntp.conf` (`restrict`/`kod`,
`disable monitor`, `nts enable`), `/etc/chrony/chrony.conf`, and
`/etc/systemd/timesyncd.conf` (client-side server steering — the Phase 5
prey).

## Operational note — your own clock

Kerberos fails on skew: before any AD attack in this chain, sync to the target's time service
(`ntpdate dc.example.com` — the line 18 observed engagements carry as tooling). The DC's NTP answer
also confirms 123/udp reachability before you commit to the Timeroasting walk.

## Reporting notes

- Version/peer leaks: Info, bundled; monlist census: Low/Medium with the client list as impact.
- Internet-facing monlist: High infrastructure finding — cite CVE-2013-5211, measured byte ratio,
  remediation (upgrade past 4.2.7p26 / disable monlist / `restrict ... noquery` / BCP 38 ingress
  filtering for the spoofing substrate).
- Timeroasting: High (oracle) / Critical (cracked account) — include RID range, hash format,
  cracked account name/password, and downstream abuse proof; remediation is rotating machine
  passwords (which breaks nothing when done right) plus treating 123/udp exposure on DCs as a
  decision, not a default.
- Never demonstrate reflection toward third parties; a size comparison between your probe and the
  response is complete evidence.

## Appendix — Tool map

- **nmap** (`-sU -p 123 -sV`; `ntp-info`/`ntp-monlist` scripts) — Phases 0-1.
- **ntpq** (`-c readvar`, `-c peers`, `-c monlist`, `-c mrulist`) — Phases 0-1.
- **ntpdate** — engagement-side clock sync (tooling, not attack) — operational note.
- **netexec (nxc)** — `-M timeroast` module: the field's harvest form — Phase 2.
- **timeroast.py / timeroast.ps1 (Secura)** — standalone RID walk — Phase 2.
- **hashcat** — mode 31300 (MS SNTP) with `--username` — Phase 2.
- Tools vault companions by name: nmap, netexec, hashcat.

CVE index: CVE-2013-5211 (ntpd monlist REQ_MON_GETLIST/REQ_MON_GETLIST_1 traffic amplification,
pre-4.2.7p26, in-the-wild Dec 2013 — NVD; amplification class tracked under CISA TA14-017A,
NTP BAF 556.9). Timeroasting is a design abuse of MS-SNTP, not a CVE — cite the Secura whitepaper.
Added 2026-11-13: CVE-2023-26551–26554 (client-side ntpq/mstolfp OOB, fixed 4.2.8p16 — ntp.org
Sec 3806) and CVE-2023-26555 (Palisade driver praecis_parse, Sec 3807); CVE-2023-33192
(ntpd-rs NTS-cookie crash, pre-0.3.3); CVE-2025-58066 / GHSA-4855-q42w-5vr4 (ntpd-rs
1.2.0–1.6.1 server response-loop storm, fixed 1.6.2).
