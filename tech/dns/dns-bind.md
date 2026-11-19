---
technology: "BIND (ISC)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: authoritative DNS server (named, 53/tcp+udp)
status: thin
standards: "RFC 1034/1035, RFC 2136 (UPDATE), RFC 2845 (TSIG)"
homepage: "https://www.isc.org/bind/"
provenance: "corpus-grounded (3 writeups: a leaked TSIG key infra.key → nsupdate record injection → hostname-keyed SSH; a path-traversal read of /etc/bind/named.conf.local + named.conf → allow-transfer ACL + zone file path; a bind9:9.18 docker config with allow-transfer none as lab reference) + upstream-verified (ISC KB aa-00307 version.bind/version none; ISC BIND 9 ARM config semantics; NVD CVE-2020-1350 for the Windows-side contrast). Thin per evidence gate (3 files ≥ 2 = thin); companion area router: [[dns|DNS]]. Enrichment pass 1, 2026-11-12."
---

# BIND (ISC) — implementation methodology

Companion router: [[dns|DNS area router]] (protocol-common AXFR/enum/nsupdate patterns, ADIDNS
section). This page covers what changes when the master is ISC BIND: its config tree, its
key files, its ACLs, and its version oracle. Target model: `named` on Linux (`/etc/bind/`,
Debian layout), zones in `/var/lib/bind/`, TSIG keys for dynamic update.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Zone transfer (AXFR) | allow-transfer ACL too broad | yes | Internal namespace + record data — High |
| TSIG key leak → nsupdate | file-read primitive reaching /etc/bind/*.key | file read | Record injection/overwrite — High |
| Nonsecure dynamic updates | zone `allow-update any` / missing key | yes | Record injection — High |
| version.bind disclosure | default config | yes | Version pin for CVE matching — Low/Info |
| named.conf exposure | LFI/path traversal into /etc/bind/ | LFI | ACLs, key paths, zone list — Medium |
| Recent named CVEs | version pinned vulnerable | varies | RCE/DoS — verify per CVE upstream |

## Phase 0 — Fingerprint

```bash
nmap -sV -p 53 198.51.100.10            # "53/tcp open domain ISC BIND 9.11.3-1ubuntu2"
                                        # dns-bind-version NSE: |_ bind.version: 9.11.3-...
dig @198.51.100.10 CH TXT version.bind +short   # manual CHAOS-class query, same string
```

ISC's KB confirms: BIND answers `version.bind` (class CHAOS, type TXT) with the real version
unless the operator sets `version none;` or a decoy string in the `options` block, or
overrides the built-in chaos zones with a restrictive view. Treat a clean version string as
a free CVE index over the ISC advisory history — then verify the specific CVE against
upstream (never trust version→CVE lists from memory).

## Phase 1 — Zone data recon

AXFR is the goal; the ACL decides. `allow-transfer` defaults to none on modern samples
(a reference config shipped `allow-transfer { none; };`) but per-client and
per-subnet grants are common leftovers (a real deployment shipped `allow-transfer { 10.0.0.0/8; }` —
any pivot into that range transfers the zone).

```bash
dig axfr example.com @198.51.100.10
dig axfr internal.example.com @198.51.100.10   # every zone named in SOA/NS chains
```

Zone files themselves live under `/var/lib/bind/` (Debian: `db.<zone>`), referenced from
`named.conf.local`. Any file-read primitive (LFI, backup exposure) that reaches
`/etc/bind/named.conf` cascades: it names the included files (`named.conf.options`,
`named.conf.local`), the zone list, the transfer/update ACLs, and the TSIG key paths.

## Phase 2 — Config exposure and key theft (BIND-specific)

One observed chain: path traversal `....//....//.../etc/bind/named.conf.local` → zone
block reveals `file "/var/lib/bind/db.example.internal";` and `allow-transfer { 10.0.0.0/8; }`,
plus an `update-policy`/key reference → read `/etc/bind/named.conf` for the `include` of
the key file → TSIG secret in hand. With the key, the DNS server is a record-writer:

```bash
nsupdate -k /etc/bind/infra.key
> server 198.51.100.10
> zone example.com
> update add attacker.example.com 300 A ATTACKER_IP
> send
```

One observed chain shows the payoff shape: records added with the leaked `infra.key` redirected
a hostname whose SSH access was keyed on name resolution (login as the `bindmgr` account),
and the same account's sudo rule ran a script rooted in `/etc/bind/named.conf.bindmgr` —
the BIND config tree doubling as the privesc surface. Lesson: after any key/config win,
enumerate what *trusts the names* (SSH host aliases, service endpoints, backup jobs) —
BIND is rarely the final target, it is the redirector.

## Phase 3 — Dynamic update without a key

If the zone allows nonsecure updates (or the update ACL is `any`), skip the key theft
entirely — plain `nsupdate` from the protocol-common page works. Test non-destructively
with a probe record under a name you control, then clean it with `update delete`.

## Phase 4 — Version-gated CVE work

Bind the version string to the ISC advisory archive, not to recall. Notable classes:
recursive-resolver DoS/cache issues, RRSIG parsing (compare SIGRed CVE-2020-1350 — a
Windows DNS, not BIND, bug that attackers routinely misattribute to BIND; version.bind
tells you which side of that mistake you are on).

## Command quick reference

```bash
dig CH TXT version.bind @198.51.100.10            # version oracle
dig axfr example.com @198.51.100.10               # zone pull
named-checkconf /etc/bind/named.conf              # with any shell: config validation enumerates includes
nsupdate -k /etc/bind/infra.key                   # TSIG-authenticated writes
rndc status                                       # with shell on host: running config + zones
```

## Tool mapping (prose)

dig for CHAOS/AXFR/UPDATE probing; nsupdate for record writes; nmap with the
dns-bind-version NSE for banner sweeps; dnsenum/dnsrecon for subdomain dictionaries
against the BIND-resolved namespace; metasploit auxiliary scanner modules for AXFR
checks. Tools vault owns per-tool detail.
