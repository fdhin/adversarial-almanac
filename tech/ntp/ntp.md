---
technology: "NTP"
kind: overview
audience: offensive-security
family: network-services
layer: time synchronization (UDP/123; ntpd/chronyd daemons; mode 6 control queries)
standards: "RFC 5905 (NTPv4); MS-SNTP AD authentication extension"
status: thin
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-ntp.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "command-context re-gate (2026-11-12): 23 files match, but real attack evidence is 1 writeup (Timeroasting — netexec -M timeroast against DC, $sntp-ms$ RID-keyed machine-account hashes -> crack); 4 files are 123/udp nmap banners only, 18 are ntpdate clock-sync usage (client convenience, not surface attacks). Zero ntpq/monlist usage in corpus. Upstream-verified: NVD CVE-2013-5211 (monlist amplification, exploited in the wild Dec 2013, fixed 4.2.7p26); ntpq(1) man page (mode 6, peers/readvar/mrulist); CISA TA14-017A (NTP BAF 556.9); Timeroasting research (Tom Tervoort / Secura — MS-SNTP MD5(MD4(pwd)||response) offline oracle, hashcat 31300). Enrichment pass 1, wave 2."
---

# NTP

> The daemon everyone allows through the firewall — a pre-auth UDP oracle that has served both the largest amplification attacks ever measured and, on Windows domains, an unauthenticated hash-extraction endpoint for every machine account.

## What it is

NTP (Network Time Protocol, RFC 5905) keeps clocks synchronized over UDP/123. The classic daemon is **ntpd** (Network Time Foundation / University of Delaware lineage; the modern alternative is chronyd/chrony). To an operator the service is three surfaces. **Time service**: mode 3 client / mode 4 server packets, no authentication in the default deployment. **Mode 6 control queries** via `ntpq`: read variables (`readvar`/`rv` — associd 0 is the system variables: version, processor, os, stratum, peers), list peers (`peers`/`-p`), and on old daemons the monitor list — `monlist`, the request that returns up to the last ~600 client addresses the daemon heard from. **Mode 7 private requests** (the legacy monlist transport). Nothing in the default config requires authentication for any of this: the standard ntpd access control is `restrict` lines, and the common deployment leaves `noquery` off.

The two headline abuse classes are both pre-auth and both verified upstream. **Amplification**: CVE-2013-5211 — the monlist feature in ntp_request.c (ntpd before 4.2.7p26) answers a forged-source `REQ_MON_GETLIST` with a response dozens of times larger than the request; CISA TA14-017A measures NTP's bandwidth amplification factor at **556.9x**, and it was exploited in the wild in December 2013. Modern ntp replaced monlist with `mrulist` (rate-limited, nonce-protected) — but the internet still runs old daemons. **Domain hash extraction**: on Active Directory, Microsoft's MS-SNTP extension authenticates NTP responses with a MAC keyed by the requesting **computer account's** NT hash — but the requester picks the RID and need not authenticate. **Timeroasting** (Tom Tervoort, Secura, 2024) walks RIDs against a DC, harvesting `MD5(NT-hash || NTP-response)` values for every machine account in crackable form (`$sntp-ms$` hashcat mode 31300) — the single observed chain turned this into machine-account passwords and a foothold.

The operator takeaway: **123/udp answering is a pre-auth oracle plus a reflection hazard** — version and peer disclosure by default, monlist amplification on anything pre-2014-patch, and on DCs a domain-wide machine-account hash dump that costs one UDP packet per RID. The daemon's own clock also matters operationally: Kerberos dies on skew, so attackers and engagers alike end up syncing with `ntpdate` before touching AD — 18 observed engagements carry exactly that line, which is usage, not attack.

## The anatomy that matters on an engagement

    client ── mode 3 (time request) ──>  ntpd :123/udp
    ntpq ── mode 6 (control) ─────────>  readvar 0 (version/processor/stratum) · peers (upstream map)
         └─ legacy: monlist (~600 recent client IPs)  <- CVE-2013-5211 amplifier, BAF ~557x
    spoofed source ── monlist ──> daemon ── floods victim          (reflection/DoS lane)
    AD lane: crafted MS-SNTP request with chosen RID ──> DC
         └─< response MAC = MD5(NT-hash(machine acct) || response) -> offline crack (Timeroasting)

Three structural properties drive most findings:

- **Everything answers without auth by default** — time, control queries, and (on old builds) monlist; `restrict ... noquery` is the only gate and is routinely absent.
- **The daemon remembers its clients** — monlist/mrulist data is a pre-auth census of everything that asked this host for time (internal IP lists, NTP-client fleets); on the public internet that same memory is the amplifier.
- **The protocol's integrity model was bolted on** — symmetric keys/autokey are rare in deployment, and Microsoft's RID-keyed MAC, meant to stop clock-spoofing MitM, instead lets an anonymous requester pull keyed material for any account (Timeroasting): the auth design itself is the oracle.

## Where you'll meet it

- **Domain controllers** (AD environments) — w32time/MS-SNTP answering on 123/udp: the Timeroasting lane; harvest machine-account hashes, crack weak/default pre-2K passwords, win a machine identity (one observed engagement's whole entry chain).
- **Public NTP pools and stratum servers** — anything answering monlist pre-4.2.7p26 is a 556.9x reflector waiting to be listed in amplification botnets; the finding is infrastructure abuse exposure.
- **Internal networks generally** — ntpq enum leaks version/OS/processor and the upstream time topology (peers), plus (legacy) the client census; feed host lists into the scan plan.
- **Adjacent operational role** — clock skew breaks Kerberos; expect `ntpdate <target>` in your own workflow before AD attacks, and know it appears constantly in the field as tooling, not as a target surface.

## Reading the daemon's mind

1. **`ntpq -c readvar` is a version/OS banner in UDP**: associd 0 returns the system variables including `version`, `processor`, `system`, and `stratum` — fingerprint material with zero authentication.
2. **`ntpq -c peers` maps the upstream topology** — the daemon tells you who it syncs to (refids, strata); that's internal-infrastructure recon and pivot hints (who else runs time, what the core routers sync to).
3. **monlist vs mrulist dates the daemon** — `monlist` answering means pre-4.2.7p26 (CVE-2013-5211, reportable amplifier); modern builds offer `mrulist` (rate-limited, requires an interactive nonce — a "current" answer by itself is a version tell).
4. **A DC's 123/udp is a hash oracle, not just time** — MS-SNTP response MACs are keyed by machine-account NT hashes, keyed by an attacker-chosen RID; every RID is one datagram (Timeroasting).
5. **UDP means spoofable and untrackable** — reflection attacks are inherent to the transport; on the defense side, ingress filtering (BCP 38) is the only real answer, and on engagements your monlist probe should be scoped, not sprayed.
6. **chrony's command plane is a different port** *(added 2026-11-13)* — chronyd does not speak ntpq mode 6 at all: its monitoring protocol rides **323/udp**, loopback-only by default but admin-exposable (`bindcmdaddress` + `cmdallow`), leaking sources/clients/serverstats when opened — scan it separately. And where NTS is deployed, a second surface exists: the **TLS 1.3 NTS-KE channel on 4460/tcp** — a standard TLS endpoint to fingerprint. Both lanes are in [[ntp-bug-bounty|the methodology]].

## Where to go next

[[ntp-bug-bounty|NTP bug bounty methodology]] — ntpq enumeration, monlist/CVE-2013-5211 verification, and the Timeroasting harvest-crack chain.
