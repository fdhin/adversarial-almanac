---
technology: "NTLM (NT LAN Manager)"
kind: overview
audience: offensive-security
family: directory-services
layer: "authentication protocol (challenge–response over SMB/HTTP/LDAP/MSSQL/...)"
language: "n/a (protocol [MS-NLMP]; attack tooling Python/C)"
homepage: "https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-nlmp/b38c36ed-2804-4868-a9ff-8dd3182128e4"
github-repo: "https://github.com/lgandx/Responder (capture) · https://github.com/fortra/impacket (relay)"
provenance: "corpus-grounded (re-gated command-context gate — 38 files / 461 machines: Responder invocation 21, NetNTLM hash capture/crack 12 (hashcat -m 5600, john netntlmv2, $NETNTLM strings), relay tooling (ntlmrelayx/MultiRelay/smb_relay) 7, PTH technique lines 9; the structure-pass hint of 6 was a ~6x undercount — its marker set (ntlmrelayx, responder, pass-the-hash, pth-*) missed the hash-capture family and prose-form Responder lines; a further 42 files carry netexec 'signing:True/False' probe lines tracked as context-only, not gating; PyTorch/Python '.pth' filename matches excluded by design) + upstream-verified: [MS-NLMP] Learn page (v38.0, 2026-11-10 — 'used by application protocols to authenticate remote users and, optionally, to provide session security'), impacket ntlmrelayx.py source header (server list SMB/HTTP/WCF/RAW/RPC/WinRM/WinRMS/MSSQL/RDP; 'only way to stop this attack is … SPN checks and or signing'), Responder README (LLMNR/NBT-NS/mDNS poisoning, rogue servers, NetNTLMv1/v2 + Kerberos AS-REQ hashcat -m 7500, analyze mode), PetitPotam README (MS-EFSRC coercion, CVE-2021-36942), hashcat example table (5600 NetNTLMv2 / 5500 NetNTLMv1+ESS / 1000 NTLM). Companion methodology: [[ntlm-bug-bounty|NTLM bug bounty methodology]]."
---

# NTLM

> The protocol that authenticates with a hash and no server identity check: whoever can stand up a service on the network can collect challenge–responses, and wherever signing is optional the same authentication can be replayed against a third party as the victim.

## What it is

NTLM is Windows' legacy challenge–response family ([MS-NLMP]): the server sends a random challenge, the client proves possession of the NT hash (MD4 of the password) in its response, the server validates — by asking a DC (domain) or checking locally (SAM). Three properties make it the default internal-attack surface. First, **the hash IS the credential**: any captured NT hash authenticates as its owner (pass-the-hash) — no plaintext ever needed, which is why the [[ntds]] unit's dumps and this unit's captures feed the same moves. Second, **the client authenticates to whatever answered**: name-resolution poisoning (LLMNR/NBT-NS/mDNS) turns a typo'd share name into credential capture — Responder's whole model, run in 21 observed machines. Third, **the server-side check is optional**: without signing/channel-binding, a relayed response authenticates the *attacker's chosen target* as the victim (ntlmrelayx — 7 observed machines; the impacket source comment says the only real stops are SPN checks and signing).

The capture/relay split defines the workflows. **Capture** (Responder) yields crackable NetNTLMv2 (`hashcat -m 5600`) or v1 (5500 — trivially crackable via crack.sh-style rainbow tables when the challenge is fixed: an observed engagement sets Responder's challenge to `1122334455667788`) — a *password* win. **Relay** (ntlmrelayx + a coercion primitive like PetitPotam) authenticates to a target of your choosing while it's live — an *access* win, no cracking, working even when hashes are uncrackable. Modern estates kill relay targets one by one (SMB signing, LDAPS channel binding, EPA on web enrollment — [[ad-cs]] ESC8), so the unit's target list is really an audit of which mitigations are absent.

## The anatomy that matters on an engagement

    poison:  LLMNR / NBT-NS / mDNS  (Responder)   ← who answers name lookups?
    capture: rogue SMB/HTTP/MSSQL/LDAP/... server → NetNTLMv1/v2 → hashcat 5500/5600
    coerce:  PetitPotam (MS-EFSRC) · PrinterBug (MS-RPRN) · SCF/.lnk on shares → forced auth
    relay:   ntlmrelayx servers (SMB/HTTP/RPC/WinRM/MSSQL/WCF/RAW/RDP) → target of choice:
               smb://   needs signing NOT required  (probe: netexec '(signing:False)')
               ldap:// → RBCD writes, ACL abuse (--delegate-access; [[ad-acl]])
               http:// endpoints (AD CS web enrollment = ESC8 → [[ad-cs]])
               mssql://, winrm:// (exec), rpc:// ...
    pth:     NT hash → direct auth (impacket --hashes · evil-winrm -H) — capture-agnostic
    crack:   NetNTLMv2 -m 5600 · v1 -m 5500 · NT -m 1000 (from [[ntds]]/[[windows-secrets]])

Three structural properties drive most findings:

- **The challenge is server-chosen, the response is offline-crackable.** NetNTLMv2 = HMAC-MD5(challenge ∥ material, NT hash) — weak passwords fall to rockyou at mode 5600 speed; v1 with a fixed challenge falls to precomputation outright.
- **Signing posture is per-service and auditable.** The netexec probe line prints `(signing:True/False)` for every host (42 observed engagements); every False on SMB (or LDAP without sealing requirements, or an HTTP endpoint without EPA) is a live relay lane.
- **Coercion converts "user clicked nothing" into authentication.** MS-EFSRC/MS-RPRN methods make *machines* authenticate (PetitPotam needs no credentials against a DC — README-verified; CVE-2021-36942 was an incomplete patch), so relay victims include DCs, not just humans.

## Where you'll meet it

- **Flat corporate VLANs** — Responder on any position harvests hash-spray material within minutes.
- **File-share-heavy estates** — .scf/.lnk/.url plants on writable shares (observed: share-plant artifacts plus a SQL `xp_dirtree`-style UNC fetch) force authenticated SMB from whoever browses.
- **AD CS deployments** — ESC8 relay is the flagship.
- **ACL-abuse chains needing a write primitive** — relay-to-LDAP with `--delegate-access` mints the RBCD machine account (observed in the field; see [[ad-acl]]/[[kerberos]]).
- **NTLM-disabled or signing-enforced estates** — the [[kerberos]] unit takes over; Kerberos-only tooling (getTGT + `-k`) is the workaround.

## Reading the protocol's mind

1. **`(signing:False)` on a member host in the netexec probe = relay lane**; on DCs signing is default-required (one observed engagement notes DC signing enforced while a member web host was not) — always relay to members/CAs, not DCs, for SMB.
2. **A captured `user::DOMAIN:challenge:response:…` line is crackable, not passable** — NetNTLMv2 ≠ NT hash; only relay (live) or PTH (needs the real NT hash) authenticates.
3. **v1 responses (`$NETNTLM$…`, field-observed) mean downgrade-able clients** — with a fixed challenge they crack en masse; if you control the rogue server's challenge, keep it `1122334455667788`.
4. **Kerberos AS-REQ hashes from Responder (hashcat -m 7500)** are the bonus capture class when clients refuse NTLM (README-verified feature).
5. **`ntlmrelayx -socks` holding sessions open** turns a one-shot coerced auth into a menu (observed: a SOCKS-then-http relay chain) — relay windows are short; keep the connection alive instead of re-coercing.

## Where to go next

[[ntlm-bug-bounty|NTLM bug bounty methodology]] — Responder setup with challenge tuning, capture→crack matrix (5600/5500/7500), coercion primitives, ntlmrelayx target selection with signing/EPA checks, PTH forms, and the cross-unit rights that gate LDAP relays.
