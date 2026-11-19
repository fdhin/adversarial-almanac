---
technology: "SIP (Session Initiation Protocol)"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "evidence-grounded THIN (2 files, fingerprint-tier only; see internal coverage ledger) + upstream-verified: RFC 3261, EnableSecurity SIPVicious OSS README + SVCrack wiki page, nmap NSE sip-methods doc, 2026-11-12 — every attack step below is upstream-derived; no evidence-base exemplar exists yet"
provenance: "corpus-grounded: 3 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[sip|SIP overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# SIP Bug Bounty Methodology

**Scope:** SIP over UDP/TCP 5060 (TLS 5061) against PBX registrars/proxies (Asterisk-family and proprietary). **Out of scope:** RTP media interception (different tooling), the PBX's web/AMI/SSH management planes (treat as separate Linux/web surfaces once reached), WebRTC.

**Authorization:** presumes written permission, and VoIP needs it spelled out: a successful registration can place billable calls. **DESTRUCTIVE** flags anything that can place calls, leave registrations, or lock accounts. Toll fraud liability is real — many programs exclude it; check before Phase 4.

**Honest-evidence note:** the evidence base carries fingerprint-tier SIP evidence only. The chain below is the upstream-standard VoIP audit (SIPVicious toolset, RFC 3261 semantics); treat expected outputs as documented behavior, not observed behavior.

## Decision tree (one screen)

1. UDP/TCP 5060 answers a probe → Phase 1 (discover + method surface).
2. Registrar answers REGISTER with 401 → Phase 2 (extension enumeration).
3. Extensions known → Phase 3 (digest cracking: dictionary / numeric range / defaults).
4. Valid extension + password → STOP unless calls are explicitly in scope (Phase 4 note).
5. TLS-only on 5061 → same chain, transport-wrapped; point the tools with the TLS flag.

## Phase 1 — Discovery and method surface

```bash
nmap -sU -sV -p5060 198.51.100.10          # observed: 'Asterisk PBX' flagged on default port
nmap -sU --script sip-methods -p5060 198.51.100.10
# output shape (upstream doc):
#   5060/udp open  sip
#   | sip-methods: INVITE, ACK, CANCEL, OPTIONS, BYE, REFER, SUBSCRIBE, NOTIFY, INFO
sipvicious_svmap 198.51.100.0/24           # ranged SIP server discovery (console-script name; legacy: svmap)
sipvicious_svmap -p 5060,5061,8080-8100 198.51.100.10   # port ranges per README
```

Read: the `Allow:` header (from OPTIONS — that is how sip-methods works, upstream) is the method map; `REGISTER` presence = registrar (Phase 2 applies); `INVITE` without auth hints at open relay (fraud surface — note, don't exercise). A realm string in any 401 (`Digest realm="asterisk"`) fingerprints the PBX family.

## Phase 2 — Extension enumeration (svwar)

```bash
sipvicious_svwar -e100-999 198.51.100.10          # numeric extension range
```

Mechanism (upstream README): svwar "identifies working extension lines on a PBX — a working extension is one that can be registered" and reports whether each requires authentication. The oracle is response-code divergence: exists-but-needs-auth (401 + challenge) versus not-present (404) — no password attempts needed, so this phase is low-risk. Expected result: an extension list with auth-required flags; unauthenticated-registerable extensions are immediate findings (someone left a line open).

Session hygiene: `-s name` saves the session (README), letting you resume (`--resume`) — keep scans resumable so the engagement can pause.

## Phase 3 — Digest authentication cracking (svcrack)

```bash
sipvicious_svcrack -u100 -d dictionary.txt udp://198.51.100.10:5060   # dictionary against extension 100
sipvicious_svcrack -u100 -r1-9999 -z4 198.51.100.10                  # numeric range, 4-digit zero-padded
sipvicious_svcrack -u100 -D 198.51.100.10                            # default/weak password scan (--enabledefaults)
sipvicious_svcrack -u 100 -n 198.51.100.10                           # nonce-reuse speedup (wiki)
```

Upstream-verified modes (SVCrack wiki): `-u` target username/extension; `-d` dictionary (or `-` stdin); `-r` numeric ranges (`100-200,300-310,400`) with `-z` zero-padding; `-D` enable-defaults; `-n` nonce reuse (replay-based speedup); `-p` destination port/ranges; `-t` throttle. svcrack works against registrar and proxy servers per the README. PBX-specific guidance: try 4-6 digit paddings matching the extension plan before dictionaries; default sets (per `-D`) first — appliances ship documented defaults.

**Offline note:** where the program prohibits online guessing, capture one challenge/response exchange (your own REGISTER attempt against a known extension) and crack the digest offline with hashcat's SIP digest modes — same material, no server load. Tools named in prose in Appendix A.

## Phase 4 — Post-credential boundaries (STOP-and-check)

With a valid extension/password you can REGISTER and place calls. **DESTRUCTIVE and usually out of scope**: billing starts at the first INVITE out. The reportable finding is the credential itself plus (if permitted) a single controlled voicemail access or internal extension-to-extension call. Do not dial out, do not leave registrations active, do not touch auto-attendants. Toll-fraud validation belongs to the client's fraud team, not the tester.

## Phase 5 — Adjacent surfaces once the PBX host is interesting

- **Asterisk manager interface (AMI)** on the appliance — a separate authenticated TCP service (config files: `manager.conf`, `sip.conf`, `extensions.conf` on the host) — treat as post-shell target material.
- **Web admin panels** (FreePBX-style) — standard web methodology.
- **Desk phones** — individual handsets run SIP stacks and web configs with their own defaults; the same digest logic applies per-line.
- No forced cross-links: SIP digest cracking is self-contained (there is no relay-to-Kerberos analogy worth claiming — NTLM relay belongs to [[ntlm]] and ticket attacks to [[kerberos]]; SIP authenticates with digest challenges, not NTLM/Kerberos tickets, so those chains do not transfer).

## Phase 6 — Defense and validation notes

- **Extension-enum finding:** the svwar list + response-code divergence shown for a few lines.
- **Cracked credential finding:** extension + evidence of the 200 OK registration, with the password redacted in the report body.
- **Detection telemetry defenders should have (note and avoid):** OPTIONS/REGISTER bursts from single sources, 404 storms (enumeration signature), repeated 401s to one extension, registrations from unknown IPs/ASNs.
- **Hardening for the report:** strong per-extension secrets (long random, not extension-derived), fail2ban-style lockouts on 401 thresholds, restrict 5060 to voice VLAN/trunks, disable unauthenticated registrations, TLS (5061) with digest over it, separate inbound/outbound auth, international-call caps for fraud containment.

## Appendix A — Tooling (named in prose)

- **SIPVicious OSS (EnableSecurity)** — svmap (server discovery over ranges/ports), svwar (extension enumeration), svcrack (digest cracking: dictionary/numeric/defaults, nonce reuse), svreport (session export), svcrash; Python 3, installs `sipvicious_*` console scripts (upstream README).
- **nmap** — `-sU` UDP service detection plus the `sip-methods` NSE (Allow-header enumeration, default/safe category); sip-brute and related NSEs exist for credential work at volume.
- **sipcrack** — classic offline digest-auth cracker for captured SIP challenge/response exchanges (wordlist-driven); pair with a capture of your own REGISTER attempt.
- **Wireshark** — SIP dissector for reading the 401 challenge fields (realm/nonce/algorithm) and verifying your probes on-path.
- **Asterisk CLI** — on-box `asterisk -rx "sip show peers"`-style verification when host access is in scope.
- References: RFC 3261; SIPVicious README and SVCrack usage wiki; nmap NSE sip-methods doc (all upstream-verified for this page).

## Appendix B — Quick reference

```bash
# discover + methods
nmap -sU -sV -p5060 198.51.100.10 ; nmap -sU --script sip-methods -p5060 198.51.100.10
sipvicious_svmap 198.51.100.0/24
# extensions
sipvicious_svwar -e100-999 198.51.100.10 -s extenum
# digest crack: defaults → range → dictionary
sipvicious_svcrack -u100 -D 198.51.100.10
sipvicious_svcrack -u100 -r1-9999 -z4 198.51.100.10
sipvicious_svcrack -u100 -d dictionary.txt udp://198.51.100.10:5060
```
