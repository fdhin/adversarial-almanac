---
technology: "IPsec/IKE VPN"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: "VPN key exchange and network-layer crypto (IKE/ISAKMP on UDP 500, NAT-T UDP 4500, ESP/AH payloads)"
standards: "RFC 2409 (IKEv1), RFC 7296 (IKEv2), RFC 3947 (NAT-T), RFC 7383 (IKEv2 fragmentation)"
status: "upstream-only — bucket-2 new-unit, zero corpus evidence by design"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/ipsec-ike-vpn-pentesting.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: rewritten from HackTricks per the third-party-reference rule, with every load-bearing claim re-verified against primary sources — NVD/CERT VU#857035 + Felsch et al., USENIX Security 2018 for CVE-2018-5389 and the IKEv1 Bleichenbacher CVE family; Check Point sk185033 for CVE-2026-50751; ZDI/TrendAI advisory for CVE-2026-33824; ikeforce flag semantics grounded against the tools vault ikeforce note. Zero corpus evidence by design (new-unit wave 3, 2026-11-13)."
---

# IPsec/IKE VPN pentesting

Target model: an IKE daemon answering on UDP 500 (and UDP 4500 once NAT
traversal kicks in) — site-to-site concentrators, remote-access VPN
gateways, firewall VPN blades. Everything here is about the VPN
**endpoint and its key exchange**, not about breaking ESP after the fact.
This is the IPsec world: not [[openvpn]] (TLS-based, X.509-centric, its
own unit) and not [[pptp]] (PPP over GRE with MS-CHAPv2) — see the
contrast section at the bottom before cross-applying techniques.

Related pivoting pages once a tunnel is up: [[socks-bug-bounty|SOCKS]]
and [[turn-bug-bounty|TURN relay]].

## Transit vs endpoint — pick your attack frame first

An IPsec target can be approached three ways, and confusing them wastes
engagement time:

1. **Transit (on-path).** You can observe or intercept the wire: capture
   an IKEv1 Aggressive Mode exchange and crack the PSK offline; stand up
   a fake responder to harvest XAuth usernames/passwords. Precondition:
   a position between client and gateway (authorized only, always).
2. **Endpoint (the gateway itself).** Pre-auth parsing bugs in the IKE
   daemon: Vendor ID handling, IDi/certificate payload parsing in
   IKE_SA_AUTH, fragment reassembly. This is memory-corruption territory
   (see CVE-2026-33824 below) and needs appliance-version intelligence.
3. **Post-auth (the inner network).** The VPN's real prize is the
   network behind it. A working group name + PSK + user credential set
   is a foothold: treat a successful connection as the start of internal
   enumeration, not the finish line.

## Phase 0 — Confirm the service

```bash
nmap -sU -p 500,4500 198.51.100.10
nmap -sU -p 500 --script ike-version 198.51.100.10
```

UDP 500 open is ISAKMP; UDP 4500 means NAT-T is in play and some
gateways answer *only* there — probe both. Silence on 500/4500 is not
proof of absence: aggressive rate limiting and drop-on-bad-proposal
behavior look identical to no service.

## Phase 1 — Find a valid transform (IKEv1)

An IKEv1 policy accepts only specific transform combinations
(encryption, hash, auth method, DH group, lifetime). Until you name one
the gateway accepts, most further work stalls. The classic tool is
ike-scan: by default it sends main mode with a single proposal carrying
eight transforms and prints what comes back.

```bash
ike-scan -M 198.51.100.10
```

Read the summary line at the end of the run:

- `1 returned handshake; 0 returned notify` — the gateway negotiates
  IKE and at least one proposed transform was acceptable; the accepted
  SA line (Enc/Hash/Group/Auth) is your transform.
- `0 returned handshake; 1 returned notify` — IKE is alive but every
  transform was rejected; enumerate proposals one by one.
- `0 returned handshake; 0 returned notify` — inconclusive: filtering,
  loss, rate limiting, or a silent-drop implementation. Do not call this
  "no VPN".

Generate proposal space and walk it (rate-limit this — it is a huge
probe set and includes museum-piece algorithms; use only where
authorized):

```bash
for ENC in 1 2 3 4 5 6 7/128 7/192 7/256 8; do
  for HASH in 1 2 3 4 5 6; do
    for AUTH in 1 2 3 4 5 6 7 8 64221 64222 64223 64224 65001 65002 65003 65004 65005 65006 65007 65008 65009 65010; do
      for GROUP in 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18; do
        echo "--trans=$ENC,$HASH,$AUTH,$GROUP"
      done
    done
  done
done > ike-dict.txt

while read t; do
  (echo "hit: $t" && sudo ike-scan -M $t 198.51.100.10) \
    | grep -B14 "1 returned handshake" | grep "hit:"
done < ike-dict.txt
```

If main mode stays silent even for sane transforms, retry the same walk
in aggressive mode:

```bash
while read t; do
  (echo "hit: $t" && ike-scan -M --aggressive -P aggr.txt $t 198.51.100.10) \
    | grep -B7 "SA=" | grep "hit:"
done < ike-dict.txt
```

Transform notation is `--trans=$Enc,$Hash,$Auth,$DH`. DH groups worth
knowing by number: 1 = 768-bit MODP, 2 = 1024-bit, 14 = 2048-bit,
15 = 3072-bit. Groups 1 and 2 are obsolete; seeing them accepted
identifies an appliance from another era and predicts the rest of its
security posture. `AUTH=PSK` in the response is the finding that makes
the whole aggressive-mode crack chain (Phase 3) available.

ikeforce automates the same discovery and more (flag semantics grounded
against the tools vault ikeforce note): `ikeforce 198.51.100.10 -a -s 1`
cycles accepted transform sets and prints them; feed one back as
`-t <enc> <hash> <auth> <dh>` in later phases. The isaudits `iker.py`
script drives ike-scan through the same loops if you prefer that
harness.

### Fingerprint the implementation

Retransmission backoff timing plus Vendor ID (VID) payloads usually
identify the vendor:

```bash
ike-scan -M --showbackoff 198.51.100.10
```

The backoff-pattern table ends in an implementation guess (e.g. a Cisco
concentrator); treat it as a hypothesis to corroborate, since firmware,
proxies and packet loss distort timings. nmap's `ike-version` NSE gives
a second opinion.

Vendor quirk worth knowing: some IKEv2 daemons embed appliance version
data in non-standard VID payloads of the IKE_SA_INIT response — one
WatchGuard Fireware format is a 32-byte hash followed by base64 that
decodes to `VN=<version> BN=<build>`. Single-packet, pre-auth version
disclosure:

```bash
echo 'Vk49MTIuMTEuMyBCTj03MTk4OTQ=' | base64 -d   # -> VN=12.11.3 BN=719894
```

Not an RFC behavior — a vendor fingerprint you can use to scope
vulnerable builds before sending any exploit traffic.

## Phase 2 — Find the group name (ID)

With a transform in hand, the next unknown for remote-access VPNs is the
identity/group name the gateway expects. This is only enumerable where
the gateway behaves differently for valid and invalid IDs — many
deliberately do not, and then this phase is dead; move to sniffing or
credentials you already hold.

```bash
# Does a fake ID produce a hash at all?
ike-scan -P -M -A -n fakeID 198.51.100.10
```

If random IDs get no hash but some candidate does, that difference is
your oracle. Walk a group-name wordlist (SecLists carries an
ike-groupid.txt; the ikeforce repo ships groupnames.dic):

```bash
while read id; do
  (echo "Found ID: $id" && sudo ike-scan -M -A -n $id 198.51.100.10) \
    | grep -B14 "1 returned handshake" | grep "Found ID:"
done < ike-groupid.txt
```

ikeforce's enumeration mode (`ikeforce 198.51.100.10 -e -w groupnames.txt -s 1`)
tries several behavioral distinguishers of its own — Cisco Dead Peer
Detection responses, response-count differences, `INVALID-ID-INFORMATION`
notifications — after first characterizing the server with random IDs.
Expect false positives/negatives; the ike-scan loop above is the more
conservative instrument.

The cheap alternative when you are on-path: **sniff the ID**. In
aggressive mode the client's identity goes out in the clear in message 1
(Network Security Assessment, 3rd ed.), so a capture of a real client
connection hands you the group name without touching the gateway.

## Phase 3 — Capture and crack the PSK (aggressive mode)

Aggressive mode + PSK + a valid group name = an offline-crackable hash,
today exactly as in the original ERNW pskattack writeup:

```bash
ike-scan -M -A -n <group_id> --pskcrack=hash.txt 198.51.100.10
psk-crack -d wordlist.txt hash.txt          # ike-scan's own cracker
# or: convert with ikescan2john and run john, or hashcat directly:
hashcat -m 5400 hash.txt wordlist.txt       # IKE-PSK SHA-1
hashcat -m 5300 hash.txt wordlist.txt       # IKE-PSK MD5 — match the negotiated hash
```

Aggressive mode also leaks the *client's* identity pre-auth (the ID must
travel in the clear so the gateway can pick the right PSK when multiple
groups/users exist) — check captured or elicited handshakes for
`ID(Type=ID_USER_FQDN, ...)` lines.

A cracked group PSK is a shared secret, not a user password: record it
as reusable key material and test it against other services only if the
rules of engagement cover credential reuse.

### Main mode is not safe either: CVE-2018-5389

The belief that only aggressive mode falls to offline dictionary attacks
was disproved in 2018. CVE-2018-5389 (CVSS 8.8, CERT VU#857035; Felsch,
Grothe, Schwenk et al., USENIX Security 2018, "The Dangers of Key
Reuse"): an **active** attacker who interferes with the DH exchange —
acting as a responder and waiting for the victim initiator to connect —
obtains a MAC value computed from public session data, the DH shared
secret and the PSK, and can grind the PSK offline. The same key-pair
**reuse across versions and modes of IKE** additionally enables
cross-protocol authentication bypasses (impersonating a host or network).
The paper's Bleichenbacher-oracle family broke the RSA-encryption IKEv1
modes (PKE/RPKE) and, through cross-protocol reuse, RSA-signature auth
in IKEv1 and IKEv2: Cisco (CVE-2018-0131), Huawei (CVE-2017-17305),
Clavister (CVE-2018-8753), ZyXEL (CVE-2018-9129). Defensive consequence
worth reporting: high-entropy PSKs *and* disabling PKE/RPKE modes are
both required; either alone leaves a door open. (Upstream-history
context — not corpus-grounded.)

## Phase 4 — XAuth: the user layer behind the group PSK

PSK typically authenticates the *group*; XAuth (a vendor extension
between phase 1 and 2, usually backed by AD/RADIUS) authenticates the
*user*. IKEv2 replaces XAuth with EAP. Two attacks:

- **On-path credential capture.** Redirect IKE traffic (ARP spoofing)
  into a fake VPN endpoint — fiked is the classic fake-IKE daemon — and
  read the XAuth username/password pairs as clients connect. Blocking
  UDP 500/4500 should make a correct client fail closed; traffic that
  silently falls back to a non-VPN path is a separate fail-open finding.
- **Online guessing** once group ID + PSK are known
  (`ikeforce 198.51.100.10 -b -i <group_id> -u <user> -k <PSK> -w passwords.txt -s 1`,
  or `-U` with a user list). This is live authentication guessing
  against a directory-backed service: respect lockout policy and the
  engagement's rate constraints.

## Phase 5 — Connect (vpnc)

With group ID, PSK and XAuth credentials, establish the tunnel and pivot
(`/etc/vpnc/<profile>.conf` with `IPSec gateway`, `IPSec ID`,
`IPSec secret`, `IKE Authmode psk`, `Xauth username/password`; run
`vpnc <profile>`). From here the engagement is internal enumeration
through a new interface — the gateway stopped being the target.

## Appliance-era CVEs to check against fingerprints

- **Check Point CVE-2026-50751** (sk185033, severity High, exploited in
  the wild): user-authentication bypass in Remote Access / Mobile Access
  IKEv1. Reachable only when **all** of: remote access enabled, IKEv1
  enabled for remote access, legacy clients accepted, machine
  certificates not mandatory. Fixes ship as Jumbo Hotfix takes across
  R81.10–R82.10. If fingerprinting lands on a Check Point gateway, the
  presence of legacy-client support is itself a reportable configuration
  finding even where the hotfix is installed.
- **Windows IKEEXT CVE-2026-33824** (ZDI/TrendAI): pre-auth double free
  in IKEv2 fragment reassembly — a Security Realm Vendor ID blob
  allocated during IKE_SA_INIT is shallow-copied into a queued work item
  for a reassembled fragmented IKE_AUTH and freed twice (packet-context
  teardown, then SA teardown). Unauthenticated, UDP 500/4500, crash to
  potential code execution. Vendor guidance: block inbound 500/4500
  where IKE is not needed; restrict to known peers otherwise. The audit
  pattern generalizes: **fragment reassembly + reinjection + async work
  queues are one attack surface** — deep-copy vs shallow-copy field
  audits of queued packet contexts find this class.
- WatchGuard Fireware IKEv2 has had pre-auth parsing research published
  (2025, watchTowr) — version-gate via the VID trick above before
  touching exploit paths.

Default-proposal intelligence for scoping: appliances that still accept
3DES/SHA-1/DH-2 proposals are telling you their config predates modern
guidance; on IKEv2-only endpoints expect AES-256/SHA-2 with DH 14+ and
treat anything weaker in an accepted SA as a hardening finding.

## Distinguishing IPsec/IKE from OpenVPN

| Signal | IPsec/IKE | [[openvpn]] |
|---|---|---|
| Ports | UDP 500/4500 (NAT-T), ESP | UDP 1194 typically, TCP variants exist |
| Handshake | ISAKMP binary; transforms, DH groups, VIDs | TLS; certificates or static keys |
| Credential attack | Aggressive-mode PSK hash capture (ike-scan), group-name enumeration, XAuth layer | TLS client-cert / user-pass via auth plugin; no transform hunting |
| Endpoint bugs | Daemon parser memory corruption (VID, IDi, fragmentation) | OpenVPN server/plugin CVEs, management interface |
| Finding shape | Weak PSK + aggressive mode + group guess | Weak user creds, cert handling, privileged helper bugs |

Cross-applying tools (trying ike-scan against an OpenVPN box or vice
versa) only burns time — fingerprint first.

## Tool appendix (prose)

ike-scan (transform discovery, backoff fingerprinting, PSK-hash capture,
ships psk-crack); ikeforce (transform/group enumeration, XAuth brute
force, full negotiation; Python 2 era, unmaintained upstream); iker.py
(isaudits; ike-scan driver); hashcat modes 5300/5400 and john with
ikescan2john; fiked (fake IKE endpoint for XAuth capture); vpnc (client);
nmap NSE ike-version.
