---
technology: "PPTP"
kind: overview
audience: offensive-security
family: network-services
status: "thin (2 writeup files pass the re-gated CHAP-family command-context gate — upstream carries the 1723/GRE surface)"
layer: VPN tunnel protocol — PPP over GRE (IP proto 47), control channel on 1723/tcp
homepage: "https://poptop.sourceforge.net/"
provenance: "corpus-grounded THIN (re-gated 2026-11-12: 2 writeup files; structure-pass count of 3 was bare-digit noise — three candidates matched only a hex string, a float, and a JWT epoch containing '1723', all out; the corpus holds ZERO literal 'pptp'/1723-control-channel attacks, evidence is the shared MS-CHAPv2 credential-attack family: one file (hostapd-wpe rogue AP captures MSCHAPv2, hashcat -m 5500), the other (crack.sh returns NT hash 'plugged back into the chapcrack tool') — see internal coverage ledger) + upstream-verified: crack.sh PPTP page (chapcrack workflow; DES jobs incl. PPTP VPNs/LM-NTLMv1/WPA-Enterprise), moxie0/chapcrack (2012), Microsoft Security Advisory 2743314 (2012, unencapsulated MS-CHAP v2), willhackforsushi asleap (LEAP/MS-CHAPv2 dictionary attack), nmap pptp-version NSE docs, RFC 2637, 2026-11-12."
---

# PPTP

> A VPN whose whole security reduces to one MS-CHAPv2 handshake — and that handshake is a single DES key wearing the user's password as a costume (Moxie Marlinspike & David Hulton, Def Con 20, 2012).

## What it is

PPTP (Point-to-Point Tunneling Protocol, RFC 2637, 1999 — Microsoft, ECI Telecom, and friends) tunnels PPP sessions over IP: a **control channel on 1723/tcp** (connection setup, call management, echo) and a **data channel over GRE, IP protocol 47**. The useful mental model for an operator: it is PPP dial-up networking lifted onto an IP network, so everything about PPP authentication and IPCP negotiation rides along, most importantly **PAP, CHAP, MS-CHAPv1 and MS-CHAPv2**, with MPPE (RC4) encryption keyed directly from the MS-CHAPv2 handshake.

The design's fatal property, formalized at Def Con 20 in 2012 ("Practical Attacks against MS-CHAPv2", Marlinspike with Hulton): the MS-CHAPv2 response encodes the NT hash under **three DES keys where the third is derived from known constants** — the exchange carries the strength of *one* DES key against the full password space. The chapcrack tool parses captured handshakes, strips the two crackable DES challenges, and the crack.sh service solves the remaining DES problem in under a day. Microsoft's own response (Security Advisory 2743314, 2012) was not a patch — it was guidance to stop using unencapsulated MS-CHAPv2. Crack one handshake and you own the password, and with it the MPPE session keys and the tunnel.

In the observed record, PPTP as a *service* (1723/GRE) never appears — the evidence is the **CHAP/MS-CHAPv2 credential-attack family** that PPTP shares with WPA2-Enterprise (PEAP/MS-CHAPv2) and with NTLMv1 challenge/response: a rogue-AP capture cracked with hashcat mode 5500 (one engagement), and a crack.sh job whose NT hash feeds chapcrack (the other). Those chains transfer to PPTP verbatim; the 1723/GRE surface below is upstream-carried, unobserved.

## The anatomy that matters on an engagement

    port scan → 1723/tcp open (pptp)
      → Start-Control-Connection → vendor/version banner      ← fingerprint fork
      → PPP negotiation → auth method fork:
           PAP (cleartext password)          ← capture = done
           CHAP/MS-CHAPv1                    ← legacy, weak
           MS-CHAPv2 challenge/response      ← the 2012 single-DES collapse
      → GRE (IP proto 47) data channel, MPPE RC4 keyed from the handshake
      → crack the handshake → password → re-authenticate → decrypt MPPE

Three structural properties drive most findings:

- **The tunnel's security is one authentication exchange.** GRE/MPPE add no independent secret — every key derives from the MS-CHAPv2 response. Break the handshake, own everything (upstream: the 2012 research; crack.sh advertises PPTP VPNs as a standard DES job).
- **MS-CHAPv2 is a family name, not a PPTP feature.** The identical exchange (NT hash, DES challenge/response, crack.sh-solvable) appears in WPA2-Enterprise inner auth, Cisco LEAP, and NTLMv1 logins — tooling and cracks transfer across all of them (observed: one engagement cracks the WPA-Enterprise variant with hashcat -m 5500; the other cracks the NETNTLM variant via crack.sh + chapcrack).
- **The control channel is chatty and unauthenticated.** 1723 answers Start-Control-Connection requests from anyone with vendor and firmware fields — nmap's pptp-version script exists precisely for this — while the GRE data channel is invisible to TCP-only tooling (protocol 47 must be allowed through the path, which NATs frequently break, a good tell for "VPN appliance behind misconfigured firewall").

## Where you'll meet it

- **Legacy VPN concentrators and firewall/router remote-access** — PPTP survives on ancient appliance firmware and embedded gear; a 1723 banner with an old vendor string dates the whole device.
- **Windows RRAS exposure** — a domain's oldest remote-access path; MS-CHAPv2 cracks feed directly into credential reuse against the domain.
- **As the CHAP family, everywhere else** — enterprise Wi-Fi (PEAP/MS-CHAPv2 inner auth), LEAP legacy, NTLMv1-capable SMB stacks: the same crack.sh/asleap/hashcat toolbox applies (both observed engagements).

## Reading the protocol's mind

1. **`nmap -p1723 --script pptp-version`** returns Vendor/Firmware from the control channel before any authentication — the fingerprint and era check in one packet (upstream: nmap NSE docs).
2. **PAP in the auth negotiation means cleartext passwords** on the wire — no cracking needed, just capture.
3. **MS-CHAPv2 means a crack.sh-shaped target**: any capture (rogue AP, on-path, pcap trove) reduces to a DES job; chapcrack formats it (upstream: crack.sh PPTP workflow).
4. **MPPE traffic is decryptable after the crack** — keys derive from the same handshake, so the handshake capture is also the decryption key (2012 research).
5. **GRE (proto 47) through your relay** fails on NAT paths — if the target's PPTP works through a NAT box, that box is doing GRE-aware helpers worth mapping.

## Where to go next

[[pptp-bug-bounty]] — the phased agent methodology: control-channel fingerprinting, auth-method fork, handshake capture patterns, the CHAP-family cracking toolbox (chapcrack/crack.sh, asleap, hashcat modes), and GRE/MPPE aftermath.
