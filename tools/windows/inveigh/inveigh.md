---
name: "inveigh"
categories:
  - windows
homepage: "https://github.com/Kevin-Robertson/Inveigh"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: dotnet
license: see repository
meta-checked: "2026-11-13"
github-repo: "Kevin-Robertson/Inveigh"
github-match: "exact"
github-stars: 3060
gh-checked: "2026-11-13"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
last-refresh: "2026-11-19"
---

# inveigh

> Windows/mitm password capture: spoof LLMNR/NBT-NS/DNS/mDNS/DHCPv6 and harvest NetNTLM hashes through SMB, HTTP, HTTPS, WebDAV, LDAP and proxy-auth listeners.

## What it's for

Inveigh is a .NET machine-in-the-middle tool for penetration testers: it
answers name-resolution protocols (LLMNR, NBT-NS, DNS, mDNS, DHCPv6,
ICMPv6) to poison traffic towards itself, then captures NetNTLM
challenge/response hashes (and cleartext basic-auth credentials) through
protocol listeners — SMB, HTTP/HTTPS with WPAD and WebDAV, LDAP, and a
proxy-auth listener. Reach for it on an engaged Windows host to harvest
credentials the way Responder does on Linux, with everything in one
process. The primary version is the C# console app ("InveighZero"); the
legacy PowerShell version (1.506) is frozen but still in the repo. It does
not crack the captured hashes — feed them to a cracker afterwards. On
Linux/macOS builds the packet sniffer is unavailable and the SMB listener
is used instead.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Start default spoof-and-capture

Run the standard poisoned-responder session on a Windows host.

```
.\Inveigh.exe
```

Outcome: enables the LLMNR + DNS spoofers, SMB packet sniffer, and
HTTP/WebDAV/LDAP listeners with NTLM auth; every captured NetNTLM hash is
printed and appended to `Inveigh-NTLMv2.txt` (NTLMv1 and cleartext to their
own files) in the working directory. Press ESC to enter the interactive
console.

### Run listeners only (no packet sniffer)

Capture without the raw-socket sniffer — e.g. when 445 is already bound.

```
.\Inveigh.exe -sniffer n -smb n
```

Outcome: LLMNR/DNS switch to listener mode and Inveigh attempts an SMB
listener; `-smb n` suppresses the port-445 conflict error on hosts where
the SMB service already owns the port.

### DHCPv6 spoof a subnet and answer only your target domain

Poison IPv6 autoconfiguration and restrict replies to a specific domain.

```
.\Inveigh.exe -dhcpv6 y -replytodomains corp.example.com
```

Outcome: hands out IPv6 leases (making the host the subnet's DNS server)
and responds to DNS A queries only inside `corp.example.com` — queries for
other domains log `[domain ignored]`, keeping the attack quiet.

### Enable HTTPS and proxy-auth capture

Add TLS and the proxy listener for wpad/proxy traffic.

```
.\Inveigh.exe -https y -proxy y
```

Outcome: starts the HTTPS listener on 443 (default cert, override with
`-cert`/`-certpassword`) and a proxy-auth listener on port 8492 capturing
NTLM from browser proxy authentication.

### Collect captures from the interactive console

Pull hashes without stopping the session.

```
GET NTLMV2
GET NTLMV2UNIQUE
```

Outcome: entered at the console prompt (ESC toggles it) while Inveigh runs —
prints all captured NTLMv2 hashes, or one per user; the live prompt shows
counts as `C(unique:total) NTLMv1(unique:total) NTLMv2(unique:total)`.
`GET CLEARTEXT`, `GET NTLMV1` and `STOP` work the same way.

### Run on Linux

Use the cross-platform build without Windows.

```
dotnet Inveigh.dll
```

Outcome: starts the same listener set (LLMNR/DNS/HTTP/LDAP) with the packet
sniffer disabled; compile per-platform single-file binaries with
`dotnet publish --self-contained=true -p:PublishSingleFile=true -r linux-x64`.

---

## Name-resolution auditing workflow with Inveigh

**Scope note.** Inveigh is an internal-network tool, not a web target scanner. The "finding" it produces is a control gap: a Windows segment still answers legacy broadcast/multicast name resolution (LLMNR, NBT-NS, mDNS) or hands out IPv6 config (DHCPv6/ICMPv6 RA), so any host on the segment can be poisoned into authenticating to an attacker and leaking NetNTLMv1/v2. This whole section assumes an **authorized internal assessment or purple-team exercise** with written permission, a defined target subnet, and a rules-of-engagement document. Inveigh is loud and intrusive by nature — when it spoofs, it inserts itself into other machines' name resolution. The entire discipline below is about shrinking that blast radius to exactly what the engagement authorizes and nothing more. Everything here mirrors how [[responder]] is used on Linux; Inveigh is the on-host Windows equivalent, and the two are rarely run on the same segment at once (they would fight over the same queries).

The versions that matter: the C# rewrite (v2.x line, latest **v2.0.12**, built on .NET 10) is the primary tool and the one these recipes target; the legacy PowerShell build (1.506) is frozen. The C# version has **no built-in relay** — the PowerShell-era `Inveigh-Relay` is gone — so the relay demonstration in Phase 4 pairs Inveigh's poisoning with an external relay tool, lab-only.

### Phase 0 — Authorization, placement, and baseline

Goal: know exactly what you are allowed to poison and from where, and record a clean baseline before you touch anything.

- Confirm in writing: the target subnet (e.g. `198.51.100.0/24`), whether active spoofing is permitted or only passive observation, whether captured credentials may be cracked, and whether any relay demonstration is in scope. Passive-only engagements never leave Phase 1.
- Record your own host's IPv4/IPv6, MAC, and interface. Inveigh auto-assigns `-SpooferIP`/`-SnifferIP` from the primary interface; on multi-homed hosts pin them explicitly so you never answer on an out-of-scope interface.
- Elevation check: the packet sniffer and HTTPS listener require an elevated prompt; listener-only mode mostly does not. Decide which you need before you start — the sniffer binds no visible port 445 (it reads via the Windows SMB service) and is quieter, but needs admin.

### Phase 1 — Passive inspection (`-Inspect`): prove the gap without poisoning

Goal: demonstrate the exposure with zero spoofed answers. `-Inspect Y` runs every spoofer in **observe-only** mode — Inveigh logs the LLMNR/NBT-NS/mDNS/DHCPv6 requests crossing the segment but never replies. This is the equivalent of Responder's analyze mode (`-A`), and it is the correct first step on every engagement because it carries no risk of MITMing a real user.

```
.\Inveigh.exe -Inspect Y -Console 5 -LogOutput Y
```

What you are collecting: a log of who is asking for what name over which legacy protocol. A stream of LLMNR queries for `wpad`, typo'd hostnames, and departed file servers is the finding in its purest form — these are the exact queries Inveigh would have answered. Capture the log; it proves the protocols are enabled and in active use **without you having spoofed a single response**. For many assessments this log plus the remediation is a complete, defensible report on its own.

### Phase 2 — Scoped active poisoning: prove exploitability on an authorized target

Goal: convert "the protocol is enabled" into "a controlled host authenticated to me," while answering **only** your own authorized test machine. The single most important discipline here is restricting who gets a spoofed reply, so you never poison production hosts.

- `-ReplyToIPs 198.51.100.50` — answer spoofed name resolution **only** for queries originating from your authorized test box. Every other host on the segment is ignored.
- `-ReplyToDomains` / `-ReplyToQueries` — alternatively (or additionally) restrict to a specific bait domain or queried name (`-ReplyToQueries` was renamed from the older `-ReplyToHosts` in v2.0.8).
- `-RunTime 10` and/or `-RunCount 1` — cap the session to a few minutes or stop after the first capture, so an unattended tool does not keep poisoning indefinitely.

```
.\Inveigh.exe -LLMNR Y -NBNS Y -MDNS N -DHCPV6 N -ReplyToIPs 198.51.100.50 -RunTime 10 -ConsoleStatus 1
```

From your authorized test host, trigger a lookup for a non-existent name (`dir \\notareal-server\share`). Inveigh spoofs the answer, the test host connects to the SMB sniffer/listener, and a NetNTLMv2 hash for the logged-in account is captured. The console prompt increments: `C(0:0) NTLMv1(0:0) NTLMv2(1:1)>`. That captured hash, tied to a lookup you deliberately generated on an in-scope host, is the exploitability proof.

### Phase 3 — Controlled capture and evidence

Goal: collect the minimum evidence that proves the finding, then stop. Pull captures live from the interactive console (Esc toggles it) rather than scraping files mid-run:

```
GET NTLMV2UNIQUE
GET LOG
```

`GET NTLMV2UNIQUE` prints one hash per user (ideal for a report — you need one, not fifty); `GET NTLMV2` dumps all; `GET CLEARTEXT` covers basic-auth captures. File output (`-FileOutput Y`, on by default) writes `<FilePrefix>-NTLMv2.txt` etc. to `-FileDirectory`. Redact everything but the account name and the first/last few bytes of the hash in the report.

### Phase 4 — Relay demonstration (lab-only)

Goal: in a **dedicated lab**, show that a captured authentication can be relayed to a target with SMB signing disabled — the real-world escalation from "hash capture" to "code execution / data access." The C# Inveigh does not relay, so this pairs Inveigh-as-poisoner with an external relay tool (impacket `ntlmrelayx`, or Kerberos relay via [[krbrelayx]]). The trick: turn **off** Inveigh's own capture listeners so they don't swallow the auth, leaving the ports free for the relay tool.

```
# Inveigh: poison only, release 445/80 for the relay tool (lab segment only)
.\Inveigh.exe -SMB N -HTTP N -LLMNR Y -NBNS Y -ReplyToIPs 198.51.100.50 -RunTime 10
# On the relay host (Linux), impacket relays the inbound auth to a signing-disabled target:
#   ntlmrelayx.py -t smb://198.51.100.60 -smb2support
```

Never run this against a production segment: relaying authenticates a real user's session to a real target. Keep it in a lab that mirrors the client, and report the outcome as "relay is possible because SMB signing is not enforced," not by relaying production credentials.

### Phase 5 — Stop and clean up

`STOP` at the console (or `-RunTime`/`-RunCount` expiry) ends the session. Confirm the spoofers are down and no listener is still bound. Remove or securely store the capture files; they contain real credential material. Note the stop time in your log so the window of active poisoning is auditable.

### Inveigh vs. Responder — choosing the on-host tool

Both answer the same legacy protocols and capture the same NetNTLM hashes; the choice is about where you have a foothold. Pick one per segment — running both at once has them racing for the same queries.

| | Inveigh | [[responder]] |
|---|---|---|
| Platform of the running host | Windows (primary); .NET build also runs on Linux/macOS | Linux (primary) |
| Capture via native SMB service | Yes — packet sniffer reads port 445 without binding it (quieter, needs elevation) | No — binds its own listeners |
| Passive "is it enabled?" mode | `-Inspect Y` | `-A` (Analyze) |
| Scope which queries you answer | `-ReplyToIPs` / `-ReplyToDomains` / `-ReplyToQueries` (+ `-Ignore*` inverses) | `Responder.conf` + `-Analyze`, host/regex filters |
| Built-in NTLM relay | No (C# version; use external `ntlmrelayx`) | No (use `MultiRelay`/`ntlmrelayx`) |
| Interactive live console | Yes (Esc toggles; `GET …` commands) | No (tails log files) |

Reach for Inveigh when your foothold is a Windows host — especially when you want the sniffer's quieter, no-visible-bind capture. Reach for Responder when you are landing from a Linux attack box on the same segment. The methodology (inspect first, scope the replies, cap the run, redact the evidence) is identical across both.

## Where the risk is with Inveigh

The "finding" is always the enabled legacy protocol; the tool feature is how you demonstrate it. Severity below is the internal-assessment view (business risk), and the last column flags the operational hazard — the thing that gets an assessment in trouble, which replaces web bounty "dupe risk" for an internal tool.

| Finding / demonstration | Inveigh feature / flag | Severity | Operational hazard |
|---|---|---|---|
| LLMNR enabled and poisonable | `-LLMNR Y` (default on) + `-Inspect Y` to prove, scoped reply to demonstrate | High | Low if `-Inspect`/`-ReplyToIPs` scoped; MITMs real users if unscoped |
| NBT-NS enabled and poisonable | `-NBNS Y` | High | Same scoping discipline as LLMNR |
| mDNS enabled and poisonable | `-MDNS Y` | Medium–High | mDNS is chatty; unscoped replies disrupt Apple/IoT discovery |
| WPAD auto-discovery abused | HTTP/`-WPADAuth`, proxy listener (`-Proxy Y`, port 8492) | High | Can break real proxy config for clients you answer |
| NetNTLMv2 capture → offline crack | SMB sniffer / listeners, `GET NTLMV2UNIQUE` | High (if crackable) | Only crack if ROE permits; store hashes securely |
| NetNTLMv1 downgrade capture | `-Challenge 1122334455667788` (fixed challenge enables precomputation) | Critical (NTLMv1 → trivial) | Fixed challenge is a deliberate, noisy choice — lab/authorized only |
| DHCPv6 / IPv6 takeover (mitm6-style) | `-DHCPV6 Y`, `-ICMPv6 Y` | Critical | **Most disruptive** — makes you the subnet's IPv6 DNS; can break IPv6 for the whole segment |
| Relay to signing-disabled host | Inveigh poisons + external `ntlmrelayx` | Critical | Lab-only; relaying production auth is out of bounds |
| Cleartext basic-auth capture | HTTP/Proxy `Basic` auth, `GET CLEARTEXT` | High | Real passwords in logs — redact and protect |

## Command recipes

Passive-first, then tightly scoped active. Every active recipe pins who gets answered and caps the run.

```
# 1. Passive audit — observe only, no spoofed replies (always start here)
.\Inveigh.exe -Inspect Y -Console 5 -LogOutput Y
# -> logs LLMNR/NBNS/mDNS/DHCPv6 requests seen on the segment; sends nothing.
#    The log of queries IS the finding that legacy name resolution is enabled.
```

```
# 2. Scoped active proof — answer ONLY the authorized test host, cap to 10 min
.\Inveigh.exe -LLMNR Y -NBNS Y -MDNS N -DHCPV6 N `
  -ReplyToIPs 198.51.100.50 -RunTime 10 -ConsoleStatus 1
# -> spoofs replies only to 198.51.100.50; -ConsoleStatus 1 prints a capture
#    tally every minute; auto-stops after 10 minutes.
```

```
# 3. Listener-only (no raw sniffer) — e.g. when the SMB service owns port 445
.\Inveigh.exe -sniffer n -smb n -ReplyToIPs 198.51.100.50 -RunTime 10
# -> LLMNR/DNS run as listeners; -smb n suppresses the port-445 bind conflict.
```

```
# 4. NTLMv1 downgrade with a fixed challenge (authorized crack demos only)
.\Inveigh.exe -Challenge 1122334455667788 -ReplyToIPs 198.51.100.50 -RunTime 5
# -> a static challenge lets captured NetNTLMv1 be cracked via precomputation.
#    This is a deliberately aggressive, noisy setting — never on a live network
#    without explicit sign-off.
```

```
# 5. DHCPv6 / IPv6 takeover — LAB ONLY, scoped to one domain
.\Inveigh.exe -dhcpv6 y -replytodomains lab.example.com -RunTime 10
# -> hands out IPv6 leases and answers DNS only inside lab.example.com; every
#    other query logs "[domain ignored]". DHCPv6 is the single most disruptive
#    mode — it makes this host the segment's IPv6 DNS server. Lab segments only.
```

```
# 6. Drive the run from the interactive console (Esc toggles the prompt)
GET REPLYTOIPS        # prints ReplyToIPs startup values — verify ONLY your test host is listed
GET REPLYTOQUERIES    # prints the queried-name scope, if you set one
GET IGNOREIPS         # confirm production ranges are actually excluded
GET NTLMV2UNIQUE      # one hash per user — the minimal report evidence
GET CLEARTEXT         # any basic-auth captures
GET DHCPV6LEASES      # if DHCPv6 is on, list which hosts took an IPv6 lease
STOP                  # end the session cleanly
```

Verifying scope from the console **before** you generate a single bait lookup is the cheapest insurance against poisoning a production host: `GET REPLYTOIPS` / `GET REPLYTOQUERIES` / `GET IGNOREIPS` echo back the startup values Inveigh is actually enforcing, so a typo in `-ReplyToIPs` surfaces before it does damage, not after.

### Scoping and output levers (verified defaults)

Every flag below is from the v2.0.x parameter help; defaults are quoted from the README. These are the dials that turn a loud, segment-wide poisoner into a surgical, auditable test.

| Flag | Default | What it controls | Field use |
|---|---|---|---|
| `-ReplyToIPs` / `-IgnoreIPs` | All / Local | answer / ignore by source IP | Primary scope — pin replies to the authorized test host (`-IgnoreIPs` defaults to the host's own IPs) |
| `-ReplyToQueries` / `-IgnoreQueries` | All / none | answer / ignore by queried name | Bait one name; renamed from `-ReplyToHosts`/`-IgnoreHosts` in v2.0.8 |
| `-ReplyToDomains` / `-IgnoreDomains` | All / none | answer / ignore by domain | Restrict DNS answers to one in-scope domain |
| `-ReplyToMACs` / `-IgnoreMACs` | All / Local | answer / ignore by MAC (DHCPv6 only) | Scope IPv6 takeover to a single host's MAC |
| `-DNSTYPES` | A | which DNS record types to spoof (A, AAAA, SOA, SRV) | Answer only `A`; omit `AAAA` to avoid breaking IPv6 resolution |
| `-NBNSTypes` | 00,20 | which NBNS suffix types to spoof (00,03,20,1B) | Narrow NBT-NS noise to the suffixes you need |
| `-MDNSTypes` | A | which mDNS record types to spoof (A, AAAA, ANY) | Narrow mDNS noise |
| `-Repeat` | Enabled | re-spoof a host after a capture | Set `N` to capture once and stop hammering the same host |
| `-Local` | Disabled | spoof the host system itself | Leave off — avoids self-poisoning the box you run on |
| `-IPv4` / `-IPv6` | Enabled / Enabled | master on/off per IP family | `-IPv6 N` on IPv4-only engagements keeps you off IPv6 entirely |
| `-ConsoleUnique` / `-FileUnique` | Enabled / Enabled | show/write only unique user+system hashes | Keep on — cuts duplicate credential handling |
| `-ConsoleStatus` | Disabled | interval (min) to auto-print the capture tally | Set `1` for an auditable running count in the log |
| `-RunTime` / `-RunCount` | Unlimited | stop after N minutes / N captures | Bound every unattended run |

Low-volume levers to standardize on: `-Inspect Y` for any recon pass; `-ReplyToIPs`/`-ReplyToDomains`/`-ReplyToQueries` (or their `-IgnoreIPs`/`-IgnoreDomains`/`-IgnoreQueries` inverses) on every active run; `-RunTime`/`-RunCount` to bound the window; `-MachineAccount N` to drop noisy machine-account captures you don't need; and keep IPv6 attack surface off unless takeover is explicitly authorized — `-DHCPV6` is off by default but `-ICMPv6` is **on by default**, so pass `-ICMPv6 N` (or `-IPv6 N` to kill the whole family) on IPv4-only engagements.

## Pitfalls, noise and scope discipline

How an Inveigh run turns into an incident report against *you*:

- **Unscoped poisoning is a production outage waiting to happen.** With no `-ReplyToIPs`/`-ReplyToDomains`/`-ReplyToQueries`, Inveigh answers *every* LLMNR/NBT-NS/mDNS query on the segment. You are now the authoritative answer for misspelled server names across the whole broadcast domain — real users get MITM'd and their name resolution breaks. Always scope to your authorized target(s) first, and prefer `-Inspect Y` whenever "is it enabled?" is the only question.
- **DHCPv6/ICMPv6 is the big red button.** `-DHCPV6 Y` makes the host the subnet's IPv6 DNS server (the mitm6 technique). On a dual-stack network this can break IPv6 connectivity for every client that takes the lease. It belongs in a lab, or behind explicit, monitored authorization with a tight `-RunTime`.
- **Credential overreach.** Capturing more hashes than you need — especially from users outside the engagement — is a scope violation even if the protocol was open. Scope the replies, cap the run, and in reports use `GET NTLMV2UNIQUE` (one per user) rather than dumping everything.
- **Cracking without permission.** Capturing a NetNTLMv2 hash and cracking it are two different authorizations. A fixed `-Challenge` plus NTLMv1 downgrade is a deliberately aggressive setup; confirm the ROE covers offline cracking before you reach for it.
- **Running Inveigh and Responder on the same segment.** They compete for the same queries and produce confusing, duplicated captures. Pick one per segment.
- **Port and privilege friction mistaken for "it's not working."** If the Windows SMB service owns 445, the sniffer path conflicts — use `-sniffer n -smb n` for listener mode. HTTPS and the raw sniffer need elevation; without it those capture paths silently do less.
- **Antivirus/EDR will flag it.** Inveigh is widely signatured. On a monitored host it may be quarantined mid-run, truncating captures and generating alerts the client wasn't warned about. Coordinate with the blue team.
- **Leaving captures behind.** The output files hold real credential material. Secure or destroy them per the engagement's data-handling rules; don't leave `*-NTLMv2.txt` on a shared host.

## Reporting evidence

What goes in the report, in order of weight:

1. **The passive proof.** The `-Inspect Y` / `-LogOutput Y` log showing LLMNR/NBT-NS/mDNS queries actually crossing the segment — this establishes the protocols are enabled *and in use* with no spoofing on your part. Include the protocol, the queried name, and the requesting host for a few representative lines.
2. **The exploitability proof.** One captured NetNTLMv2 record from your authorized test host (`GET NTLMV2UNIQUE`), redacted to the account name and a token fragment of the hash — enough to prove capture, not enough to crack from the report. State plainly that the lookup was deliberately generated on an in-scope host.
3. **The exact invocation and scope.** The full Inveigh command line you ran, including the `-ReplyToIPs`/`-RunTime` scoping, so the triager can see the test was controlled. Note the start/stop timestamps.
4. **Relay outcome (if in scope).** For a lab relay demo, the `ntlmrelayx` output showing a successful relay to a signing-disabled target — framed as "SMB signing not enforced," with no production credentials used.
5. **Remediation, specific and testable.** Disable LLMNR via GPO **Computer Configuration → Administrative Templates → Network → DNS Client → "Turn off multicast name resolution"** (writes `EnableMulticast = 0` under the DNSClient policies key). Disable mDNS by setting `EnableMDNS = 0` (DWORD) under `HKLM\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters`. NBT-NS has no single domain GPO — set `NetbiosOptions = 2` per interface under the NetBT Tcpip keys, push it via a startup script or DHCP, and disable WPAD, which falls back to LLMNR/NBT-NS. Recommend enforcing **SMB signing** to defeat relay. Note that disabling these generally has no impact where DNS is healthy, but advise testing in a controlled OU first.

## Captured hash formats and handoff

Inveigh does not crack — it writes captures in a crack-ready format and hands off. Know which you have before you move anything:

- **NetNTLMv2** (`<FilePrefix>-NTLMv2.txt`, the common case): `user::DOMAIN:<serverChallenge>:<HMAC>:<blob>`. Crack with hashcat `-m 5600` or John's `netntlmv2` format. It is salted per capture, so cracking is dictionary-bound — a strong password survives, which is a fair point to make in the report's risk rating.
- **NetNTLMv1** (`<FilePrefix>-NTLMv1.txt`, seen only when a host negotiates down or you set a fixed `-Challenge`): `user::HOST:<LMresp>:<NTresp>:<challenge>`. hashcat `-m 5500` / John `netntlm`. With the fixed challenge `1122334455667788` the response reduces to the underlying NT hash via precomputation — this is exactly why an enabled NTLMv1 is rated Critical, and why the fixed challenge is an authorized-lab-only setting.
- **Cleartext** (`<FilePrefix>-Cleartext.txt`): HTTP/Proxy Basic-auth passwords in the clear — treat as live secrets, redact in the report, destroy per the data-handling rules.

Only proceed to cracking if the ROE explicitly authorizes offline cracking; otherwise the capture record (redacted) is itself sufficient proof of the finding.

## Detecting a live poisoner (purple-team view)

The exposure Inveigh proves is also cheaply detectable, and a purple-team run should leave the blue team a repeatable test rather than just a hash:

- **Honey-query probe.** From a monitored host, resolve a random hostname that has no DNS record (e.g. `ping notareal-$RANDOM` / `Resolve-DnsName notareal-xyz`). On a clean segment the lookup fails outright; if *anything* answers the LLMNR/NBT-NS/mDNS broadcast, a poisoner — Inveigh, [[responder]], or a real attacker — is live on the wire. This is the lowest-cost standing detection for the exact gap Inveigh exercises, and it should pass (i.e. resolve nothing) once remediation lands.
- **Inspect-mode baseline as a defensive tool.** `-Inspect Y` is not only a recon step — hand it to the blue team to inventory which hosts still emit LLMNR/NBT-NS/mDNS and for which names, so remediation targets the noisiest offenders first and can be re-run to confirm the traffic stopped.
- **Endpoint signatures as a detection data point.** Inveigh and the InveighZero codebase are widely signatured; if EDR flags the binary mid-run (see Pitfalls), confirm the client's tooling actually alerted and logged it — that alert is a detection win to record, not merely an obstacle to the capture.

## References

- Inveigh repository (C# + legacy PowerShell), Kevin Robertson — https://github.com/Kevin-Robertson/Inveigh
- Inveigh README (C# parameter help, listeners, cross-platform notes) — https://github.com/Kevin-Robertson/Inveigh/blob/master/README.md
- Inveigh releases (v2.0.x history, .NET target changes, `ReplyToHosts`→`ReplyToQueries` rename) — https://github.com/Kevin-Robertson/Inveigh/releases
- Inveigh wiki (legacy PowerShell parameters, Inveigh-Relay) — https://github.com/Kevin-Robertson/Inveigh/wiki
- Quiddity protocol library underpinning Inveigh — https://github.com/Kevin-Robertson/Quiddity
- Companion Linux poisoner, same technique class — [[responder]]; Kerberos relay — [[krbrelayx]]
- Disabling NetBIOS over TCP/IP and LLMNR via GPO — https://woshub.com/how-to-disable-netbios-over-tcpip-and-llmnr-using-gpo/
- LLMNR/NBT-NS/mDNS poisoning and prevention overview — https://sbscyber.com/technical-recommendations/llmnr-nbt-ns-and-mdns-poisoning-attacks
- How to disable LLMNR in Windows Server (registry/command line) — https://www.csoonline.com/article/3449156/how-to-disable-llmnr-in-windows-server.html

*Research supplement added 2026-11-16, flags and console commands verified against the Inveigh C# README parameter help and the v2.0.12 release notes (latest, 2025-11-19, ".NET 8 → 10"; `ReplyToHosts`→`ReplyToQueries` confirmed renamed in v2.0.8), plus the Windows name-resolution hardening guidance cited above (model: claude-opus-4-8).*
