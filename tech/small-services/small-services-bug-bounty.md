---
technology: Small services (whois, ident, echo)
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: legacy-network
version: "1.1"
last-refresh: "2026-11-19"
provenance: "upstream-only; zero corpus evidence by design — query formats and tool flags grounded against RFC 1413, nmap nsedoc (auth-owners, whois-domain/whois-ip incl. whodb argument semantics), and CERT CA-1996-01 for the echo/chargen storm + rewritten from HackTricks (src/network-services-pentesting/43-pentesting-whois.md, 113-pentesting-ident.md, 7-tcp-udp-pentesting-echo.md), CC BY-NC 4.0, accessed 2026-11-13. v1.1 supplement (2026-11-19) widens the trio into the classic small-services family (chargen/daytime/qotd reflection siblings with measured BAFs), deepens ident trust-chain and whois/RDAP recon craft; amplification factors verified against US-CERT TA14-017A and NCSC-IE advisories — see Supplement block"
---

# Small services bug-bounty methodology

Companion overview: [[small-services]].

Scope: whois (43/tcp), ident (113/tcp), echo (7/tcp+udp) — enumeration, the custom-gateway injection surface, trust-oracle abuse, and reflection/amplification. Related units: [[r-services]] for the protocols that consumed ident, [[finger]] for the sibling user-disclosure service, [[memcached]] for the modern reflection family.

## Phase 0 — Fingerprint the trio

```bash
nmap -sV -p 7,43,113 198.51.100.10
nc -uvn 198.51.100.10 7        # echo: type a line, expect it back
```

For each open port, decide stock-vs-custom before choosing a phase: a whois that answers SQL-flavored errors, an identd with vendor banners, or an echo embedded in firmware each change the playbook.

## Phase 1 — whois (43/tcp): enumeration and referral chasing

```bash
whois -h 198.51.100.10 -p 43 "example.com"
printf 'example.com\r\n' | nc -vn 198.51.100.10 43
```

Test all three object types — many deployments route domain, IP/CIDR, and ASN queries (`8.8.8.8`, `AS15169`-style) to different backends or parsers. Follow referrals manually: the `Whois Server:` / `refer:` fields name the next authoritative service, and referral chains surface legacy or forgotten whois infrastructure. The standard client's `-I` flag starts at IANA and walks referrals; nmap's `whois-domain` and `whois-ip` NSE scripts automate the same for hosts and IPs, with `--script-args whois.whodb=nocache` refusing cached records so smaller delegated assignments are still found (use deliberately — the no-cache setting multiplies outbound registry queries and can earn a ban). Pivot fields when unredacted: registrar/organization/abuse contacts, creation and expiry dates for newly-registered infrastructure, nameservers for operator clustering, referral servers for forgotten estate. For Internet domain data, RDAP is the structured successor — check whether the provider also exposes it (`curl -s https://www.rdap.net/domain/example.com`); ICANN's gTLD data-access transition moved that world toward RDAP, but field-level inconsistencies between the two are documented, so compare before automating decisions on either.

### Backend injection in custom whois gateways

A whois port that fronts a registry database or proxies another directory is a parser plus a query builder. Probe the query language the backend speaks:

```bash
whois -h 198.51.100.10 -p 43 "a') or 1=1#"
```

Do not stop at SQL — internal deployments proxy to LDAP directories, shell wrappers around lookup tools, or HTTP APIs. Fuzz for SQL injection, LDAP injection, delimiter abuse, long strings, and malformed UTF-8/control characters. The protocol is trivial; the dangerous part is the glue.

### Rogue and stale whois servers

Where a registry or tool's whois hostname expires, an attacker can register it and serve answers to every client still configured to ask — published research demonstrated registering an expired whois hostname cheaply and pivoting its responses into code execution and administrative access in dependent tooling. When you find a private or legacy whois service, check whether its `refer:` values, banners, or TLD mappings point at expired or attacker-registerable domains, and treat whois-derived output rendered into web frontends as attacker-controllable text (stored XSS, parser and eval bugs downstream).

## Phase 2 — ident (113/tcp): the trust oracle

The query is a port pair, `<port-on-server>,<port-on-client>`, answered as `USERID` with an OS tag and username, or `ERROR` (`INVALID-PORT`, `NO-USER`, `UNKNOWN-ERROR`). Technique for a service you are connected to: note your source port, then ask ident about the pair. Connected to the target's file service from local source port 43218:

```bash
printf '445, 43218\r\n' | nc -vn 198.51.100.10 113
# 445, 43218 : USERID : UNIX : svc-backup
```

Sweep every open TCP port — nmap's `auth-owners` NSE does exactly this and annotates each open port with the owning account (`nmap -sV -sC 198.51.100.10` includes it in the default/safe sets); the standalone `ident-user-enum` tool takes a host and port list and prints one username per port. The output is a username harvest for password guessing against everything else — service accounts like `svc-backup` above are the high-value rows. Trust-oracle framing: RFC 1413's own security considerations state ident is unauthenticated, forgeable, and not an authorization mechanism — yet the r-services lineage and older mail daemons consumed it as a secondary trust signal (see [[r-services]]), and misconfigurations that still do are findings in themselves.

## Phase 3 — echo (7/tcp+udp): reflection and the packet storm

```bash
nc -uvn 198.51.100.10 7
hello    # comes straight back
```

TCP echo is a curiosity; UDP echo is a reflection primitive — every spoofed datagram becomes a reply to a victim, in the same amplification family as [[memcached]]. The classic force multiplier needs no spoofing at all: point a host's chargen at another host's echo over UDP and the two answer each other indefinitely — the CERT-documented packet-storm denial of service that congests both hosts and the path between them. The advisory's remediation is still the report language: disable unneeded UDP diagnostic services (echo, chargen) at inetd, and filter them at the perimeter; watch for the modern variant where one of the pair is any other UDP reflector.

## Phase 4 — Reporting the fix

Disable inetd diagnostic services (echo/chargen) outright on any host that does not demonstrably need them; filter the small-service ports at boundaries; replace whois trust with authenticated/structured RDAP lookups and sanitize whois-derived text before rendering or eval-adjacent use; for ident, either turn it off or document loudly that nothing security-relevant consumes it — and remove the consumption where it exists.

## Appendix — tools in prose

`whois` (the standard client: `-h`, `-p`, `-I`), `nc` for raw protocol exchanges in both directions, `nmap` with the `auth-owners`, `whois-domain`, and `whois-ip` NSE scripts (including `whodb` argument control), `ident-user-enum` for per-port username sweeps, and `curl` with `jq` for RDAP endpoints.

---

## Supplement — the small-services family, measured (added 2026-11-19)

*Source: external research against US-CERT TA14-017A (UDP-based amplification), NCSC-IE service-abuse advisories, RFC 862/863/864/865/867 and RFC 1413, and RDAP RFCs 9082/9083/9224/9537. Bandwidth-amplification factors (BAF) below are the published measured values, re-verified against the primary advisory. This block widens the original trio into the full inetd "small services" set, adds measured reflection math, and deepens ident and whois/RDAP tradecraft. Nothing above was rewritten.*

The trio in the scope line (echo 7, whois 43, ident 113) are three members of a larger legacy family that classic `inetd`/`xinetd` and modern systemd ship together: **echo (7), discard (9), daytime (13), qotd/quote-of-the-day (17), and chargen (19)**, each on both TCP and UDP, plus **time (37)**. They are enabled or disabled as a group, so finding one usually means finding the rest. Echo was Phase 3's reflection primitive; chargen, qotd, and daytime are its amplifying siblings, and they change the finding from "a reflector with gain ≈ 1" into "a reflector with measured, reportable gain."

### S.1 — Discover the whole family in one sweep

Probe TCP and UDP together; the version/banner probes fingerprint each service without any NSE beyond `banner`:

```bash
nmap -sT -sU -sV --version-intensity 5 \
     -p T:7,9,13,17,19,37,43,113,U:7,9,13,17,19,37 198.51.100.10
nmap -sU -sC -p U:7,13,17,19 --script banner 198.51.100.10
```

Expected fingerprints, service by service:

| Port | Proto | Service | What `-sV`/banner returns | Role |
|---|---|---|---|---|
| 7 | tcp+udp | echo | your probe bytes returned verbatim | reflector, BAF ≈ 1 |
| 9 | tcp+udp | discard | **nothing** — silently swallows input | not a reflector; port-scan noise sink |
| 13 | tcp+udp | daytime | human-readable date, e.g. `Tue Nov 19 14:32:01 2026` | small reflector |
| 17 | tcp+udp | qotd | a short quotation, newline-terminated | amplifying reflector |
| 19 | tcp+udp | chargen | 0–512 bytes of cycling ASCII per RFC 864 | **highest-gain reflector in the family** |
| 37 | tcp+udp | time | 32-bit seconds-since-1900, 4 bytes binary | tiny reflector |
| 43 | tcp | whois | query-dependent text / banner | recon + injection gateway |
| 113 | tcp | ident/auth | `USERID`/`ERROR` to a port-pair query | trust oracle |

A host answering on 13, 17, and 19 over UDP is an unpatched diagnostic stack — treat it as the whole set even if a given port filters.

### S.2 — The reflection siblings: measured amplification, single-packet discipline

The reportable property of chargen/qotd/daytime over UDP is **amplification**: a tiny spoofed request becomes a large reply aimed at a victim. You measure the factor without ever spoofing or ever touching a victim — send **one** legitimate datagram from your own address, measure the reply, divide. BAF = `UDP payload bytes returned ÷ UDP payload bytes sent` (US-CERT's definition).

```bash
# chargen (19/udp): 1-byte request, expect up to 512 bytes back
printf '\n' | nc -u -w1 198.51.100.10 19 | wc -c
# -> e.g. 512   (BAF ~= 512/1; the published figure is 358.8 averaged over real traffic)

# qotd (17/udp): short request, a full quotation back
printf '\n' | nc -u -w1 198.51.100.10 17 | wc -c
# -> e.g. 140   (published BAF 140.3)

# daytime (13/udp): fixed-format timestamp, modest gain
printf '\n' | nc -u -w1 198.51.100.10 13
# -> "Tue Nov 19 14:32:01 2026\r\n"  (~26 bytes; see note below)
```

**Published BAFs (US-CERT TA14-017A, measured over observed reflection traffic):**

| Protocol | Port | BAF | For comparison |
|---|---|---|---|
| chargen | 19 | **358.8** | between SSDP (30.8) and NTP (556.9) |
| qotd | 17 | **140.3** | ~5× the SSDP figure |
| daytime | 13 | *not listed* | small fixed response; honest estimate ≈ 10–30× over a 1-byte request, not a US-CERT-table value — report it as a measured ratio, never cite a canonical figure |
| echo | 7 | ≈ 1 | pure reflection, no gain — value is source obfuscation + the chargen↔echo loop |
| memcached | 11211 | 10,000–51,000 | the modern extreme, for context (see [[memcached]]) |

**Discipline that keeps this an authorized test, not an attack:**

- **Never spoof.** Spoofing the source *is* the DDoS. The finding is proven by the measured reply size plus internet-reachability; you demonstrate the amplifier, you never fire it at a third party.
- **One packet, then stop.** A single request establishes the ratio. Do not loop, do not script a flood, do not point chargen at echo (the CERT CA-1996-01 packet storm is a denial of service — describe it in the report, never reproduce it).
- **Reachability is part of the finding.** An amplifier only matters if a spoofed packet can reach it from the internet. Confirm the UDP port answers your probe from an off-net source address (your own testing host, not the target's LAN).

### S.3 — Report framing programs actually accept

An open UDP diagnostic port is not, by itself, a payout. Programs reject "port 19/udp is open" because it carries no demonstrated impact. A reflection finding is accepted when it carries **all three** of:

1. **A measured amplification factor** — your single-packet BAF, with the request and response byte counts shown, plus the published figure for corroboration.
2. **Internet reachability** — proof the service answers an off-network source (the host can be enrolled into a reflection botnet by anyone, not just an on-LAN attacker).
3. **A reachable victim class** — name who gets hurt: *any* UDP endpoint on the internet can be spoofed as the source, so the host is a reusable amplifier against arbitrary third parties; this is the "open resolver" framing applied to chargen/qotd.

Title it for impact: "Internet-reachable chargen (19/udp) usable as a 358.8× DDoS reflector" beats "diagnostic port open." Severity is typically Low–Medium on web-focused programs (it harms third parties, not the program's own data) but rises where the host is infrastructure (a nameserver, an edge box) whose outbound bandwidth is itself the lever. Remediation is one line: disable the inetd diagnostic services and filter 7/9/13/17/19 at the boundary for both TCP and UDP.

### S.4 — ident deepened: the trust chains, enumeration, and proxy confusion

Phase 2 established ident (113) as an unauthenticated, forgeable username oracle. The findings that pay are the systems that still *consume* that oracle as if it were authentication.

**The ident+SMTP trust chain (finding narrative).** Older MTAs — sendmail lineage especially — query ident on the connecting client and stamp the returned username into the `Received:` header (the `ident` clause), and some configurations fed it into sender-trust or relay decisions. The attack: the connecting client runs its *own* identd returning any string it likes, so the "authenticated" username in the mail trail is attacker-chosen. RFC 1413's security considerations say this outright — ident is not an authorization mechanism and must not be used as one. Report shape: find an MTA that renders ident output into headers or trust decisions, then show the same connection attributed to two different forged usernames. Impact: forged audit trail, trust-decision bypass.

**The ident+IRC trust chain (finding narrative).** IRC servers query 113 on every connecting client; a verified response is shown as `user@host`, an unverified one is prefixed `~` (`~john`). Some networks gate access, or scope bans, on the *verified* (non-tilde) ident. Two directions: (a) **server-side** — if the network keys bans or access on ident, an attacker running a forging identd evades bans and impersonates the `user@host` of another account; (b) **client-side info leak** — the IRC server's ident query against a user's machine pulls the local account name and OS tag off anyone who connects, a deanonymization primitive. Either is a finding where the trust is load-bearing.

**Enumeration via crafted queries — the response taxonomy.** The query is `<server-port>,<client-port>\r\n`; the reply is either `USERID : <opsys> : <username>` or `ERROR : <code>`. The error codes are an oracle of their own:

```bash
printf '22, 6000\r\n'   | nc -w3 198.51.100.10 113   # valid pair you hold
# -> 22, 6000 : USERID : UNIX : sshd
printf '22, 9999\r\n'   | nc -w3 198.51.100.10 113   # no such connection
# -> 22, 9999 : ERROR : NO-USER
printf '99999, 1\r\n'   | nc -w3 198.51.100.10 113   # out-of-range port
# -> 99999, 1 : ERROR : INVALID-PORT
```

| Response | Meaning | Use to the attacker |
|---|---|---|
| `USERID : UNIX : name` | the OS tag leaks the platform; name is the prize | username harvest + OS fingerprint (`UNIX`, `WIN32`, `OTHER`, `VMS`…) |
| `ERROR : NO-USER` | no such active connection | negative oracle — maps which port-pairs are live |
| `ERROR : INVALID-PORT` | port out of range / malformed | distinguishes parser rejection from NO-USER |
| `ERROR : HIDDEN-USER` | identd deliberately masks this one | masking is configured — note it, the rest may still leak |
| `ERROR : UNKNOWN-ERROR` | catch-all | often a wrapper/firewall sitting in front of identd |

The `opsys` field is a free OS fingerprint even when `HIDDEN-USER` suppresses the name. Sweep every open TCP port with `ident-user-enum 198.51.100.10 22 25 80 443 3306` or nmap's `auth-owners`; service accounts (`svc-backup`, `sshd`, `postgres`) are the high-value rows for cross-service password attacks.

**The proxy/relay confusion class.** ident answers only about connections *the host itself* terminates. On a NAT, proxy, load balancer, or multi-homed box, an ident query against a proxied connection returns the **proxy's** service account, not the real originating user — so any downstream trust that attributed actions to "the ident username" attributes them to the wrong principal entirely. Two findings fall out: (a) trust chains keyed on ident across a proxy silently mis-attribute (repudiation / forged attribution); (b) an open identd on a shared/gateway host lets you **deanonymize which local account owns an outbound connection** you can see the server side of — reverse-ident scanning, a classic on multi-user boxes.

### S.5 — whois as a recon pivot (beyond referral chasing)

Phase 1 covered referral chasing. The recon value of whois is broader: it is the entry point to **pivot graphs** across an organization's estate.

**Reverse-whois pivots.** Forward whois takes a domain and returns its registrant; reverse whois takes a registrant field and returns *every* domain that shares it. Pivot keys in order of selectivity: **registrant email** (most unique, tightest result set) → **registrant org name** → **registrant person name** → **nameserver set**. The workflow:

```bash
# 1. forward: pull the registrant fields from an in-scope seed
whois example.com | grep -iE 'Registrant (Email|Organization|Name)|Name Server'

# 2. reverse: find siblings sharing the registrant email / org
#    (reverse whois needs a dataset — WHOXY/WhoisXML/DomainTools APIs, or
#     tools built on them: DomLink, whoxyrm, whoisninja)
whoxyrm -reverse-name "Example Holdings Inc"
```

Discipline: **skip generic providers** — a reverse lookup on a shared webmail address or a privacy-service email (`contact@domainsbyproxy`) returns thousands of unrelated domains and poisons the graph; DomLink and similar tools filter these automatically. Redacted records (GDPR/privacy) break email pivots — fall back to **nameserver clustering** (operator-run NS implies common ownership) and registrar/IP-range patterns. Stay inside authorized scope: a reverse-whois pivot easily wanders into domains the program never authorized.

**Registrar abuse surface.** The registrar/reseller named in whois is itself attack surface: the registrant panel (credential attacks → domain takeover), the **abuse-contact** address (a social-engineering / reporting-channel pivot), and **expiry dates** — an in-scope domain approaching expiry whose owner forgets to renew is a takeover of the entire name, not just a subdomain. Newly-created dates flag fresh (least-reviewed) infrastructure; nameserver sets cluster subsidiaries and acquisitions that share nothing else visible. The rogue/stale-whois-server class from Phase 1 is the same idea applied to the *server* side — an expired `refer:` hostname is attacker-registerable.

**RDAP — the modern replacement and its quirks.** RDAP (RFC 9082 query format, RFC 9083 JSON responses) is whois's structured successor and is now the authoritative source for gTLD registration data. Recon advantages and quirks:

```bash
curl -s https://rdap.org/domain/example.com | jq '.entities[].vcardArray'   # follows IANA bootstrap
curl -s https://rdap.org/ip/198.51.100.10   | jq '.handle, .remarks'
```

- **Bootstrap is a free server map.** IANA's bootstrap registry (RFC 9224) tells a client the authoritative RDAP server for any TLD/IP block — `rdap.org` follows it for you. whois has no such registry; the bootstrap is itself recon intelligence (which registry runs which TLD).
- **Redaction is policy, not protocol.** RFC 9537 structures *how* redacted fields appear, but *which* fields are hidden is each registry's call — so the same domain can be fuller under one registry's RDAP than another's whois. Compare both before concluding a field is unavailable.
- **Rate limits are explicit HTTP.** RDAP returns `429 Too Many Requests` with back-off semantics (RDAP.org caps at ~10 requests / 10s for its own front end) — automatable cleanly, unlike whois's silent, inconsistent throttling.
- **Coverage gaps.** Many ccTLDs don't fully serve RDAP yet; keep port-43 whois as the fallback and reconcile field-level inconsistencies between the two before automating any decision.

### S.6 — Hardening read as attack surface

Reading the host's service config tells you what is exposed and how to word the fix. The presence of these entries *is* the finding:

```bash
# classic inetd / xinetd — the small services shipped commented-or-not here
grep -E '^\s*(echo|discard|daytime|qotd|chargen|time)' /etc/inetd.conf
ls /etc/xinetd.d/                       # echo, chargen, daytime units; "disable = yes" is the fix
# modern systemd socket-activated equivalents
systemctl list-units '*.socket' | grep -E 'echo|discard|daytime|chargen'
```

Remediation language for the report, by layer:

- **inetd/xinetd:** comment the line, or set `disable = yes` in the `xinetd.d` unit, then `systemctl reload inetd`/`xinetd`.
- **systemd:** `systemctl disable --now echo.socket chargen.socket daytime.socket discard.socket qotd.socket` — socket-activated services survive a daemon restart, so disabling the `.socket` is the real off-switch.
- **firewall:** drop **both** TCP and UDP for 7, 9, 13, 17, 19, 37 at the perimeter; restrict whois (43) and ident (113) to management ranges, or disable ident entirely and remove whatever consumed it (Phase 4).

The ordering matters in a report: disabling beats filtering (a filtered-but-running service is one ACL mistake from exposure), and for reflection the remediation and the impact are the same sentence — an internet-reachable UDP diagnostic service is an amplifier until it is off.

### S.7 — CTF notes

These services appear on CTF boxes with a different payoff profile than in a bounty program:

- **ident (113)** is the classic CTF use: a service runs as some user, and `ident-user-enum`/`nmap --script auth-owners` hands you *which* account owns each port — the hint that turns "port 3306 open" into "mysql runs as `dbadmin`, go spray that account." On HTB/THM-style boxes it is a username-harvest step feeding the next credential attack.
- **whois (43)** on a box is almost always a **custom gateway** — a whois port fronting a database is a SQL/LDAP-injection challenge (Phase 1's injection probes), not a registry lookup. A whois banner that errors on `a') or 1=1#` is the intended path.
- **chargen/echo/daytime/qotd** show up as **banner/flag carriers** — the flag, a hint, or a version string hidden in the cycling chargen output or the qotd quotation; `nc` the port and read it.
- **What differs from a bounty:** in a CTF there is one host and no third parties, so **amplification is irrelevant** — nobody pays for a reflection factor on a single box. Spoofing, BAF math, and the DDoS framing of S.2–S.3 belong only to authorized internet-facing programs. In the CTF the value is purely enumeration and the injection/banner gateway; in the bounty the reflection math is the reportable product and the enumeration is the supporting recon.

### S.8 — Evidence capture and the reflection PoC skeleton

A reflection report lives or dies on the single-packet measurement being reproducible and obviously harmless. Capture it so a triager can re-run it without touching a victim:

```bash
# 1. the measurement, byte-exact, from your own source address
REQ=$(printf '\n'); echo -n "$REQ" | wc -c           # request payload bytes
printf '\n' | nc -u -w1 198.51.100.10 19 | tee chargen.out | wc -c   # response bytes
# 2. the wire proof: one request out, one large reply in, no spoofing
sudo tcpdump -n -i any -c 2 'udp and host 198.51.100.10 and port 19' -w chargen.pcap &
printf '\n' | nc -u -w1 198.51.100.10 19 >/dev/null
# 3. BAF = response_bytes / request_bytes, computed in the report, not asserted
```

The PoC skeleton that programs accept — impact first, then the three S.3 elements, then a harmless proof:

> **Title:** Internet-reachable CharGEN (19/udp) usable as a 358.8× UDP reflector
> **Impact:** `198.51.100.10` answers unsolicited UDP on port 19 from off-network sources. A spoofed single-byte request yields a 512-byte reply (measured BAF = 512; US-CERT TA14-017A publishes 358.8 averaged over real traffic). The host can be enrolled, without authentication, as a reusable amplifier in a reflection DDoS against any UDP endpoint on the internet.
> **Reachability:** reply received at an off-net testing host (not the target LAN) — `chargen.pcap` attached.
> **Proof (non-destructive):** one legitimate (non-spoofed) datagram, reply captured; no traffic was directed at any third party.
> **Remediation:** disable the inetd/systemd diagnostic services (`systemctl disable --now chargen.socket echo.socket daytime.socket qotd.socket discard.socket`) and drop 7/9/13/17/19 TCP+UDP at the perimeter.

Evidence hygiene mirrors the rest of this methodology: capture the two packets and the byte counts, nothing more; never script the loop; and state explicitly in the report that no spoofed or victim-directed traffic was sent — the single most important sentence for a reflection finding's acceptance. The companion `time` (37) and `discard` (9) ports round out the set in a report: `time` leaks a 4-byte host clock (a weak uptime/clock-skew oracle, Info-severity), and `discard` is a non-reflector but confirms the whole diagnostic group is enabled — cite it as corroboration that chargen/qotd are not an isolated misconfiguration but the default inetd set left on.

### Supplement references

- US-CERT / CISA TA14-017A, *UDP-Based Amplification Attacks* (BAF definition and table; chargen 358.8, qotd 140.3, daytime absent) — https://www.cisa.gov/news-events/alerts/2014/01/17/udp-based-amplification-attacks
- NCSC-IE, *Internet Accessible CHARGEN Service* (BAF 358.8:1, mechanism, firewall remediation) — https://www.ncsc.gov.ie/emailsfrom/reports/ddos/chargen
- NCSC-IE, *Internet Accessible Quote of the Day (QOTD) Service* (obsolete-protocol framing) — https://ncsc.gov.ie/emailsfrom/reports/ddos/qotd/
- CERT CA-1996-01, *UDP Port Denial-of-Service Attack* (echo↔chargen packet storm) — referenced in Phase 3
- RFC 862 (echo), RFC 863 (discard), RFC 864 (chargen), RFC 865 (qotd), RFC 867 (daytime), RFC 868 (time) — IETF
- RFC 1413, *Identification Protocol* (security considerations: unauthenticated, forgeable, not for authorization) — https://www.rfc-editor.org/rfc/rfc1413
- RDAP: RFC 9082 (query), RFC 9083 (JSON responses), RFC 9224 (bootstrap), RFC 9537 (redaction) — https://www.rfc-editor.org/rfc/rfc9224
- DomLink (reverse-whois pivot, generic-provider filtering) — https://github.com/vysecurity/DomLink
- AWS, *UDP reflection attacks* (amplification-factor definition, affected protocols) — https://docs.aws.amazon.com/whitepapers/latest/aws-best-practices-ddos-resiliency/udp-reflection-attacks.html

*Supplement drafted 2026-11-19 (claude-opus-4-8); amplification factors verified against US-CERT TA14-017A and NCSC-IE advisories, ident trust-chain and RDAP behaviour against RFC 1413 and RFC 9082/9083/9224/9537, reverse-whois tradecraft against DomLink and vendor reverse-WHOIS documentation. Targets are illustrative (example.com, 198.51.100.0/24); every technique assumes an authorized program and single-packet, non-spoofed testing.*
