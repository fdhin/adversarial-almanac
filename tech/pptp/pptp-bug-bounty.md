---
technology: "PPTP"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "corpus-grounded THIN (2 writeup files; see internal coverage ledger) + upstream-verified: crack.sh/pptp (chapcrack workflow, DES job types), moxie0/chapcrack, Microsoft Advisory 2743314 (2012), willhackforsushi asleap, nmap pptp-version NSE, RFC 2637, 2026-11-12"
provenance: "corpus-grounded: 3 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[pptp|PPTP overview]] — what the protocol is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# PPTP Bug Bounty Methodology

**Scope:** PPTP services (1723/tcp control channel + GRE proto-47 data channel) and the MS-CHAPv2 credential-attack family it shares with WPA2-Enterprise and NTLMv1 (crack/credential-reuse side). **Out of scope:** L2TP/IPsec (different protocol), SSTP (TLS-wrapped PPP), plain WPA-PSK cracking (see the wireless toolbox).

**Authorization:** presume written permission. On-path capture and active credential cracking against real users is user-impacting — prefer captured-pcap analysis over live MITM where the objective allows. DESTRUCTIVE flags mark tunnel/auth-mutating steps.

**Reality note:** no observed engagement attacks 1723/GRE directly. The observed evidence is the shared CHAP family (one: MSCHAPv2 capture → hashcat -m 5500; the other: crack.sh NETNTLM → chapcrack). Phases 1-3 below are upstream-carried; Phases 4-6 are engagement-grounded.

## Decision tree (one screen)

1. `1723/tcp open pptp` → Phase 1 fingerprint (vendor/firmware = era).
2. Auth negotiation observed → PAP (capture, done) vs CHAP/MS-CHAPv1 (legacy weak) vs MS-CHAPv2 (Phase 4 crack path).
3. Handshake already in a pcap (any source) → Phase 5 direct.
4. Same MS-CHAPv2 material seen in Wi-Fi PEAP inner auth or NETNTLM context → Phase 5 applies unchanged (family transfer).

## Phase 1 — Fingerprinting the control channel

```bash
nmap -p1723 -sV 198.51.100.10
nmap -p1723 --script pptp-version 198.51.100.10   # Vendor/Firmware from the control channel
```

Read: the Start-Control-Connection reply carries vendor and firmware fields — a dated vendor string (old appliance, old pptpd) frames the era of the auth stack. GRE (IP proto 47) will not show in TCP scans; check for proto-47 pass-through with a full-protocol probe if the VPN path matters.

Expected output shape: `Firmware: 0x0100 | Vendor: <string>`.

## Phase 2 — Auth-method fork

- **PAP negotiated** → passwords cross the wire cleartext; capture is the attack (Phase 4 capture, skip Phase 5).
- **CHAP (MD5) / MS-CHAPv1** → legacy; dictionary attacks against the response are cheap (asleap context).
- **MS-CHAPv2** → the 2012 single-DES collapse applies regardless of password complexity (Phase 5).

## Phase 3 — Posture and negotiation quirks

- PPTP has no auth on the control channel itself: call setup, echo, and error codes leak before authentication (set-link-info frames, vendor echo behavior) — fingerprint material even when a login never succeeds.
- MPPE negotiation (RC4-40/RC4-128/stateless) rides the same setup; encryption strength ≠ auth strength — RC4-128 still falls to a handshake crack.

## Phase 4 — Handshake capture (on-path or pcap trove)

```bash
# on-host / on-path capture of the control + data flow
tcpdump -i eth0 -w pptp.pcap 'port 1723 or proto 47'
tshark -r pptp.pcap -Y mschapv2     # MS-CHAPv2 challenge/response frames
```

Observed analogues (family transfer, same handshake format): one chain stands up a rogue AP with hostapd-wpe and harvests the MSCHAPv2 exchange of connecting clients (`mschapv2: Mon Apr 20 ...` log lines, `phase2="auth=MSCHAPV2"` in the client profile); the other feeds an existing NETNTLM handshake to a cracking service. Its exact artifact: `crack.sh has successfully completed its attack against your NETNTLM handshake ... can be plugged back into the 'chapcrack' tool`.

## Phase 5 — Credential attack (the crack fork)

1. **chapcrack + crack.sh (upstream workflow for MS-CHAPv2/PPTP):** parse the captured handshake with chapcrack; it splits the response into its DES components and emits a crack.sh-ready job; crack.sh solves the DES problem (sub-day on their hardware) and returns the NT hash / password. With the handshake solved, MPPE session keys follow.
2. **hashcat (NETNTLM-shaped captures):** mode 5500 (NetNTLMv1) is the same DES-based exchange — observed: one engagement writes the hashcat NETNTLM hash to a file and runs `-m 5500` against rockyou-class wordlists. Dictionary-fast for weak passwords; the DES path from (1) beats wordlists for strong ones.
3. **asleap (dictionary attacks against CHAP-family exchanges):** built for Cisco LEAP, "which uses a variant of MS-CHAPv2" — feed it the capture and a wordlist; good first pass before paying for a DES solve.
4. **john** handles the same NetNTLMv1 formats where you prefer it in the cracking pipeline.

Validate the recovered credential against the service itself (a successful control-channel login) before claiming tunnel access. DESTRUCTIVE: a successful PPTP login creates real session state on the appliance.

## Phase 6 — Aftermath: GRE/MPPE

- Cracked MS-CHAPv2 → derive MPPE keys → decrypt recorded GRE data sessions (the handshake capture is also the decryption key).
- The password itself: spray it — VPN credential reuse against SSH/SMB/mail is the standard follow-through (family context: one observed chain reuses cracked material immediately; PPTP passwords are human-chosen, wordlist-adjacent).

## Phase 7 — Defense and validation notes

- **Finding shape:** fingerprint (pptp-version output) + capture provenance + cracked credential + one authenticated connection screenshot.
- **Defender telemetry to note and avoid:** control-connection floods, GRE from unmanaged peers, MS-CHAPv2 handshakes completing against rogue responders.
- **Hardening for the report:** PPTP is end-of-life guidance everywhere — migrate to modern IPsec/IKEv2 or TLS-based tunnels; where PPTP must persist, force EAP-TLS (certificate) inner auth, never MS-CHAPv2 (Microsoft's own 2743314 posture).

## Appendix A — Tooling (named in prose)

- **nmap** with the **pptp-version** NSE script — vendor/firmware fingerprint on 1723.
- **tcpdump / tshark / Wireshark** — capture and dissect the control channel and MS-CHAPv2 frames (proto 47 for GRE).
- **chapcrack** (Moxie Marlinspike) — parses MS-CHAPv2 handshakes, emits crack.sh DES jobs.
- **crack.sh** — cloud/FPGA DES-cracking service advertising PPTP VPNs, LM/NTLMv1, and WPA-Enterprise as job types.
- **hashcat** — mode 5500 (NetNTLMv1) covers the same exchange for dictionary rules.
- **asleap** — LEAP/MS-CHAPv2 dictionary attacker (willhackforsushi; superseded upstream but still serviceable).
- **john** — NetNTLMv1 cracking alternative.
- **hostapd-wpe** — rogue-AP harvesting of MS-CHAPv2 inner auth (the Wi-Fi-side source of the same handshakes).
- References: RFC 2637; nmap NSE docs for pptp-version; Microsoft Advisory 2743314; the 2012 Def Con 20 "Practical Attacks against MS-CHAPv2" research (all upstream-verified for this page).

## Appendix B — Quick reference

```bash
# fingerprint
nmap -p1723 --script pptp-version 198.51.100.10
# capture the family handshake (PPTP, or any MS-CHAPv2 source)
tcpdump -i eth0 -w pptp.pcap 'port 1723 or proto 47'
tshark -r pptp.pcap -Y mschapv2
# dictionary pass (observed pattern)
hashcat -m 5500 hash /usr/share/wordlists/rockyou.txt
# DES solve path (upstream workflow)
# chapcrack <handshake> → crack.sh job → NT hash/password → validate login
```
