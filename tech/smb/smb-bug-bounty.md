---
technology: "SMB (Server Message Block)"
kind: bug-bounty-methodology
audience: ai-agent
family: directory-services
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "researched and command-verified 2026-11-19 against primary docs: NetExec wiki (smb-protocol enumeration/authentication/spidering, smb-signing-not-required), smbmap and enum4linux-ng READMEs, Impacket examples (smbclient.py/smbserver.py), nmap NSE docs (smb2-security-mode, smb-security-mode, smb-protocols, smb2-capabilities, smb-os-discovery), Microsoft Learn (SMB signing; insecure guest logons disabled by default). Every flag below checked against the tool's current documentation; no invented flags or versions. Companion overview [[smb]]."
source: "primary-tool-documentation + Microsoft Learn (2026-11-19); house depth standard fastapi-bug-bounty.md"
---

Companion pages: [[smb|SMB overview/router]] — fingerprint-and-dispatch logic and the NetBIOS underlay. Implementation specifics live on [[smb-windows-native|Windows-native]] and [[smb-samba|Samba]]. Tool pages live in the separate tools vault (Vaults/tools); this file names tools in prose and shows their expected output.

---

# SMB Bug-Bounty & Enumeration Methodology

**Enumeration-to-abuse field guide — v1.0 (2026-11-19)**

**In scope:** the SMB wire protocol as an attack surface on an authorized engagement — dialect/signing negotiation, session setup (null/guest/NTLM/Kerberos), share and permission enumeration, the credential-handling paths over SMB, and the relay-precondition audit. Protocol-common; where behavior forks by implementation, this file dispatches to [[smb-windows-native]] or [[smb-samba]].

**Out of scope here:** the RPC pipe surface reached *through* IPC\$ (SAMR/LSAT RID cycling, secretsdump-class dumps, psexec-class exec) — those have their own units; this file stops at the point a session exists and the pipes become reachable. Also out of scope: actually *performing* an NTLM relay (it requires coercing a victim authentication — intrusive, multi-host, and rarely in a bug-bounty scope). This methodology **reports the precondition**, it does not run the attack.

**Audience & discipline:** operating under written authorization, respecting rules of engagement, avoiding destructive or high-volume tests. SMB enumeration is naturally low-volume — one NEGOTIATE, one SESSION SETUP, one TREE CONNECT per probe — and that is the point: every technique below is single-request, evidence-first, and leaves the target unchanged. Credential spraying, if permitted at all, is a handful of attempts, never a lockout-triggering flood.

---

## 1. The SMB finding model in one page

SMB is a *negotiated* protocol: the first two exchanges on a connection — NEGOTIATE (dialect + signing + encryption) and SESSION SETUP (anonymous/null/guest/NTLM/Kerberos) — fix the entire security posture of that host before a single file is touched. Almost every protocol-level finding is a property of one of those two exchanges, read off the wire. The implementation only decides *how* the misconfiguration got there (Windows policy drift vs. a Samba `smb.conf` directive — see the companion pages).

Three sentences carry most of the work:

- **The dialect table is the host's patch-posture confession.** `NT LM 0.12` (SMBv1) still answering is an era marker and a hygiene finding; SMB 3.1.1 with signing required tells you relay is dead on that host.
- **"Signing not required" is a finding you report, not an attack you run.** The misconfiguration — a signable, relayable host — is the deliverable; the relay itself needs a coerced victim and belongs to an explicitly-authorized internal assessment, not a bounty probe.
- **Authentication over SMB is a ladder, and estates skip rungs.** Anonymous → null session → guest → credentialed all ride SESSION SETUP; each rung that answers when it should not is its own finding, independent of anything above it.

### Where the findings are (triage priority)

| Priority | Finding class | What you read it from | Typical severity |
|---|---|---|---|
| 1 | Writable share holding secrets/code (RCE-adjacent) | `smbmap`/`nxc --shares` WRITE tier on a deployment/script/SYSVOL-like share | High–Critical |
| 2 | Readable share leaking credentials/PII | READ tier + spider: config files, backups, SCCM packages, `web.config`, keys | High–Critical |
| 3 | Guest/anonymous access to non-public data | null or `guest` session returning share contents | Medium–High |
| 4 | SMB signing **not required** (relay precondition) | `signing:False` per-host inventory | Medium (High in AD context) |
| 5 | SMBv1 still enabled | `NT LM 0.12` in the dialect list / `(SMBv1:True)` | Low–Medium (era/hygiene) |
| 6 | Null-session information disclosure | user/group/policy enumeration via IPC\$ pipes | Low–Medium |
| 7 | Username/session-setup error oracle | distinct `STATUS_*` codes separating valid from invalid users | Low |
| 8 | Weak/legacy auth accepted (NTLMv1, no Kerberos) | downgrade accepted, LM/NTLMv1 on the wire | Medium |
| 9 | Plaintext SMB (no SMB3 encryption) on sensitive data | passive capture shows cleartext file content | deployment-dependent |

Phases 0–1 build the per-host model; Phases 2–3 are where most payouts come from (sessions and shares); Phase 4 is credential handling; Phase 5 is the relay inventory and the one-line legacy-RCE check.

---

## Phase 0 — Fingerprint: dialect, signing, implementation in one line

Goal: confirm SMB, fix the dialect and signing state, and decide the implementation dispatch — before sending a credential.

```bash
# the one-line target model (NetExec, the maintained successor to CrackMapExec)
nxc smb 198.51.100.10
#  -> SMB  198.51.100.10  445  DC01  [*] Windows Server 2022 Build 20348 x64 \
#       (name:DC01) (domain:EXAMPLE.COM) (signing:True) (SMBv1:False)

# nmap's structured equivalent
nmap -p139,445 --script smb2-security-mode,smb-protocols,smb-os-discovery 198.51.100.10
```

Read the `nxc` banner as four fields: **build era** → exploit classes; **signing:True/False** → relay viability (Phase 5); **SMBv1:True/False** → legacy era; **name/domain** → is this a DC (signing almost always required) or a member (where signing gaps live). `nxc smb <range>` with no credentials performs only NEGOTIATE — it is the cheapest sweep in the protocol and changes nothing.

The nmap scripts give you the same facts in quotable form:

```
| smb-protocols:
|   dialects:
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2.0.2
|     2.1
|     3.0
|     3.0.2
|_    3.1.1
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled but not required
| smb-os-discovery:
|   OS: Windows Server 2022 ...
|   Computer name: DC01
|   Domain name: example.com
|   FQDN: DC01.example.com
|_  System time: 2026-11-19T...
```

`smb2-security-mode` prints one of three states per dialect — *"Message signing enabled and required"*, *"enabled but not required"*, or (SMBv1 via the older `smb-security-mode` script) *"message_signing: disabled (dangerous, but default)"*. The middle string is the relay-precondition finding. `smb-protocols` listing `NT LM 0.12 (SMBv1)` is the SMBv1-enabled finding, captured verbatim.

**Dispatch** (per the [[smb]] router): a `Samba smbd X.Y` service line, an `IPC Service (Samba …)` remark, or a Linux filer → [[smb-samba]]; a Windows build string → [[smb-windows-native]]. Everything in Phases 1–5 runs identically on both.

---

## Phase 1 — Negotiation as finding classes (SMB1/2/3)

Goal: turn the NEGOTIATE result into reportable findings. Each dialect/signing property is its own finding with its own severity; do not collapse them into "SMB is old."

### 1.1 SMBv1 (`NT LM 0.12`) still answering

SMBv1 is removed/disabled by default on modern Windows and off by default in current Samba. Its continued presence is an era marker (the MS17-010/EternalBlue generation — see 5.3) and a hygiene finding in its own right, because SMBv1 lacks pre-auth integrity and carries the legacy guest-fallback semantics. Confirm it, do not assume it:

```bash
# modern clients often refuse SMBv1 outright; force the dialect to prove it answers
smbclient -L //198.51.100.10 -N --option='client min protocol=NT1'
#  a share list back = SMBv1 is live; NT_STATUS_CONNECTION_DISCONNECTED on the
#  default client = the modern client refused SMBv1, retry as above to confirm
```

A clean NEGOTIATE at `NT LM 0.12` is the evidence. Report it as "SMBv1 enabled" with the dialect line from `smb-protocols`; severity Low–Medium on its own, escalating only if you can tie it to an unpatched build (the implementation pages own that).

### 1.2 Signing state — the relay precondition (REPORT, do not relay)

SMB signing is the single most consequential negotiation property. When signing is **required**, every message is signed with a key derived from the session, so an attacker cannot splice an authenticated session onto a different connection — NTLM relay to that host is dead. When signing is merely **enabled but not required** (or disabled), the host is relayable.

Historical default (Windows 10, Server 2016/2019, and member servers generally): signing was **required only on domain controllers** (via the default DC policy, and for SYSVOL/NETLOGON), and **enabled-but-not-required** on members and clients. That gap is why `signing:False` is endemic on member servers and workstations even in otherwise-patched estates. The newer baseline closes it: **Windows 11 24H2** (Enterprise/Pro/Education) requires both outbound and inbound signing by default, and **Windows Server 2025** requires outbound signing by default — so `signing:False` on a modern member is drift, and on anything pre-24H2 it is the default waiting to be reported.

The finding is the inventory, not the exploit. Build a per-host signing map (Phase 5 has the one-liner), report every `signing:False` host, and stop there — performing the relay needs a coerced victim authentication and is out of a bounty's low-volume, single-target discipline.

### 1.3 Encryption (SMB3) and downgrade

SMB3 offers per-share/per-session encryption (`smb2-capabilities` shows the 3.x dialect; encryption is negotiated separately). Sensitive shares served without SMB3 encryption, or a host that accepts a downgrade to an unsigned/unencrypted dialect, is a deployment finding you can only confirm with a passive capture position (6.x of the house pattern — see Phase 4.4 here). No scanner asserts "the file bytes crossed the wire in cleartext"; a tcpdump/mitmproxy observation does.

---

## Phase 2 — Session setup: null, guest, and the error oracle

Goal: walk the authentication ladder from the bottom. Every rung that answers when policy says it should not is a finding, and the *error codes* themselves enumerate users.

### 2.1 Null session vs. guest — they are different findings

The two are distinct and modern Windows treats them differently; conflating them produces wrong reports.

- **Null / anonymous session**: empty username *and* empty password. Historically this reached IPC\$ for pipe enumeration (users, groups, policy). Restricted by default on Windows since XP/2003, so a null session that still lists shares or walks pipes is itself the finding.
- **Guest session**: a *non-empty, unknown* username with an empty password, mapped to the Guest account. On Samba this is `map to guest = Bad User`; on Windows it is the Guest account being enabled.

```bash
# null / anonymous
nxc smb 198.51.100.10 -u '' -p '' --shares
smbclient -L //198.51.100.10 -N
smbmap -H 198.51.100.10               # omit -u entirely = null session

# guest (unknown user, empty password)
nxc smb 198.51.100.10 -u 'guest' -p '' --shares
smbclient //198.51.100.10/share -U 'guest%'
```

Expected outputs: `nxc` prints `[+] EXAMPLE\guest:` (guest auth live) or `[+] \:` for a successful null bind, then the `--shares` table; `smbclient -N` returns a share list or `NT_STATUS_ACCESS_DENIED`; `smbmap` prints the `Disk / Permissions / Comment` table with per-share tiers.

### 2.2 Guest semantics by Windows version (what anonymous can reach, where)

The behavior is version-gated, and the version determines whether a guest finding is "default misconfig" or "deliberate drift":

- **Insecure guest logons disabled by default** starting **Windows 10 1709 / Windows Server 2019**: the SMB2/SMB3 *client* no longer uses guest for remote access and no longer falls back to guest after invalid credentials. Windows 10/11 **Home** (and older) still allow guest. Windows 11 Pro (Insider 25267 and later) also blocks guest connections to a remote share by default.
- The server-side consequence a bounty cares about: a Windows **server** with the Guest account enabled and a guest-readable share is still reachable by a client that *you* control (your tooling does not honor the client-side block), so a guest-readable share on a server is a finding regardless of the client default. The client-side default only means legitimate Windows users were not already walking in.
- The failed-guest signature on a locked-down client is **event 31017** ("Rejected an insecure guest logon") and the user-facing *"your organization's security policies block unauthenticated guest access"* / error `0x80070035`. The GPO is **Enable insecure guest logons** (Computer Config → Admin Templates → Network → Lanman Workstation), i.e. `Set-SmbClientConfiguration -EnableInsecureGuestLogons`.
- NetExec (v1.3.0+) added explicit guest-session detection — the probe line surfaces guest reachability alongside `(Null Auth:...)`, so the fingerprint already tells you which rung to test.

Report guest/null access by naming the exact data it reached, not merely that it was accepted: "anonymous session lists and reads `\\198.51.100.10\Backups` (contains `web.config` with a DB connection string)" is a finding; "guest enabled" alone is Info.

### 2.3 Session-setup error-oracle enumeration

SESSION SETUP returns distinct `NT_STATUS_*` codes that leak account state without any valid credential — a user-enumeration primitive that costs one authentication attempt per name:

| Response to `user` + bogus password | Meaning |
|---|---|
| `STATUS_LOGON_FAILURE` | user exists, password wrong (or generic deny) |
| `STATUS_ACCOUNT_DISABLED` | user exists, account disabled |
| `STATUS_ACCOUNT_LOCKED_OUT` | user exists, now locked — **stop**, you are near the lockout policy |
| `STATUS_PASSWORD_EXPIRED` / `STATUS_PASSWORD_MUST_CHANGE` | user exists, valid credential state |
| `STATUS_ACCOUNT_RESTRICTION` | user exists, logon-hour/workstation restriction |
| `STATUS_NO_SUCH_USER` | user does **not** exist |

The oracle is the *difference* between `STATUS_NO_SUCH_USER` and `STATUS_LOGON_FAILURE`. Where both valid and invalid names return the same code, the oracle is closed. Enumerate gently — a few names, never a password loop against each (that trips lockout, code 9 above). Pull the password policy first so you know the lockout threshold:

```bash
nxc smb 198.51.100.10 -u 'guest' -p '' --pass-pol   # lockout threshold / window / min length
```

Treat the oracle as Low severity on its own; its value is as an amplifier feeding a *single*, policy-safe spray in Phase 4.

---

## Phase 3 — Share enumeration and permission mapping

Goal: the money phase. Turn "a session exists" into a ranked list of shares with READ/WRITE tiers and the sensitive files inside them.

### 3.1 Map every share to a permission tier

```bash
# per-share READ/WRITE with whatever creds you have (null, guest, or validated)
smbmap -H 198.51.100.10 -u guest -p ''
nxc smb 198.51.100.10 -u 'user' -p 'Pass' --shares
nxc smb 198.51.100.10 -u 'user' -p 'Pass' --shares READ,WRITE   # filter to the tiers that matter
```

`smbmap` output — the tier is the product:

```
[+] IP: 198.51.100.10:445   Name: dc01.example.com      Status: Authenticated
        Disk                      Permissions     Comment
        ----                      -----------     -------
        ADMIN$                    NO ACCESS       Remote Admin
        C$                        NO ACCESS       Default share
        IPC$                      READ ONLY       Remote IPC
        NETLOGON                  READ ONLY       Logon server share
        SYSVOL                    READ ONLY       Logon server share
        Deployments               READ, WRITE     SCCM packages
```

The tiers map straight to severity: **NO ACCESS** = out of reach; **READ ONLY** = exposure finding (what's inside?); **READ, WRITE** = the top of the queue. A `WRITE` on a share that feeds code execution — logon scripts under `NETLOGON`/`SYSVOL`, an SCCM/`Deployments` package source, a scheduled-task or web-root share — is the RCE-adjacent finding at the top of the triage table. Prove *write* non-destructively: upload a single uniquely-named empty marker to a path nobody uses, confirm it lists, delete it, and record all three steps. Never overwrite existing files to "prove" write.

### 3.2 Spider the readable shares for secrets

READ access is only a finding once you name what it exposed. Spider for the recurring high-value shapes — config files, backups, `web.config`/`.config`, `unattend.xml`, `*.kdbx`, private keys, scripts with embedded credentials, and the classic **SCCM package / share-cache** finds (deployment shares routinely hold install scripts with service-account passwords):

```bash
# NetExec built-in spider: pattern-match filenames across a share
nxc smb 198.51.100.10 -u 'user' -p 'Pass' --spider 'Deployments' --pattern 'xml','config','ps1','bak'

# spider_plus module: enumerate (and optionally download) every readable file
nxc smb 198.51.100.10 -u 'user' -p 'Pass' -M spider_plus
nxc smb 198.51.100.10 -u 'user' -p 'Pass' -M spider_plus -o DOWNLOAD_FLAG=True

# smbmap: recursive listing + regex auto-grab (needs -r)
smbmap -H 198.51.100.10 -u 'user' -p 'Pass' -r 'Deployments' --depth 3 -A '(password|secret|connectionstring)'
```

`spider_plus` writes a JSON inventory of readable files (metadata only unless you set `DOWNLOAD_FLAG`); keep the inventory as evidence and download only the one or two files your PoC needs. The `smbmap -A` regex auto-downloads matches — scope it tightly so you do not pull more data than the proof requires.

### 3.3 Full-enumeration pass (users/groups/policy via one tool)

`enum4linux-ng` (the Python3/YAML rewrite of `enum4linux.pl`, wrapping `nmblookup`/`net`/`rpcclient`/`smbclient`) consolidates the session-setup and share picture with the pipe-level enumeration in one run:

```bash
enum4linux-ng -A 198.51.100.10                       # users,groups,shares,OS,policy,printers,nmblookup
enum4linux-ng -A -u 'guest' -p '' 198.51.100.10      # same, as guest
enum4linux-ng -R -r 500-600,1000-1100 198.51.100.10  # RID cycling (NOT in -A; opt in explicitly)
enum4linux-ng -A 198.51.100.10 -oY out               # YAML evidence file (out.yaml)
```

`-A` covers the simple enumeration (`-U -G -S -P -O -N -I -L`); **RID cycling is deliberately not in `-A`** — enable it with `-R` and set ranges with `-r` (default `500-550,1000-1050`). The RID/pipe surface itself belongs to the rpc unit; here it is corroborating evidence for the share and session findings.

---

## Phase 4 — Credential handling over SMB

Goal: understand which credential paths the host accepts, and validate credentials you already hold — without tripping lockout.

### 4.1 NTLM vs. Kerberos paths

SMB authenticates via NTLM (challenge/response, works against an IP) or Kerberos (ticket-based, requires the hostname and realm, not an IP). The distinction matters operationally and as a finding:

```bash
# NTLM (works by IP)
nxc smb 198.51.100.10 -u 'user' -p 'Pass'

# Kerberos (needs FQDN + realm; no IP)
nxc smb dc01.example.com -u 'user' -p 'Pass' -k
smbclient //dc01.example.com/share -U 'EXAMPLE\user' --use-kerberos=required
```

If NTLM authentication is available at all, Kerberos can be skipped; where an estate has *disabled* NTLM, tooling must switch to Kerberos (hostname-based). A host still accepting **NTLMv1** (or LM) is a downgrade finding — weak, crackable, and relayable — distinct from the signing finding and worth its own line.

### 4.2 Pass-the-hash — and its constraints

SMB accepts an NT hash in place of the cleartext password (NTLM proves possession of the hash, not the password), so a hash you already hold authenticates directly. The flag differs per tool — verify which you are using:

```bash
# NetExec: -H takes LM:NT or just the NT half
nxc smb 198.51.100.10 -u 'Administrator' -H 'aad3b435b51404eeaad3b435b51404ee:<NT>'
nxc smb 198.51.100.10 -u 'Administrator' -H '<NT>'

# smbmap: PtH goes in -p as LMHASH:NTHASH (there is no -H for hash; -H is the host)
smbmap -H 198.51.100.10 -u 'Administrator' -p 'aad3b435b51404eeaad3b435b51404ee:<NT>'

# impacket smbclient.py (binary may be impacket-smbclient): -hashes LMHASH:NTHASH
impacket-smbclient EXAMPLE/Administrator@198.51.100.10 -hashes 'aad3b435b51404eeaad3b435b51404ee:<NT>'
```

Constraints to state in a report, because they bound the finding's reach: pass-the-hash is an **NTLM** technique — it does **not** traverse the Kerberos path (that needs overpass-the-hash/ticket techniques, a separate unit), so an NTLM-disabled host blunts it. The NT hash alone is sufficient (the LM half can be the blank `aad3b435…` constant). And a non-admin hash still only reaches what that account's ACLs allow — "valid hash" is not "admin."

### 4.3 Credential validation (the guest/null sweep, and its history)

The cheapest high-value SMB finding has always been "credentials that should not work, do." The sweep one-liner evolved: `smbclient -L -N` for null sessions → CrackMapExec's `cme smb <range> -u … -p …` → today's maintained **NetExec** `nxc smb <range>`. The guest/unrestricted-guest variant — an unknown username with an empty password against a whole range — historically turned up guest-readable estates in one line, and still does:

```bash
# validate a credential you hold across a range (low-volume: ONE password)
nxc smb 198.51.100.0/24 -u 'user' -p 'Pass' --continue-on-success --shares

# the guest sweep: unknown user, empty password, whole range
nxc smb 198.51.100.0/24 -u 'guest' -p '' --continue-on-success --shares
```

`[+]` lines are validated principals; `--continue-on-success` keeps the sweep going past the first hit. **Lockout discipline**: this is credential *validation* (one known password, or the empty-password guest case), not spraying. If you spray at all, pull `--pass-pol` first (2.3), stay well under the lockout threshold, and never loop passwords against a single account. A lockout you cause is an incident, not a finding.

### 4.4 On the wire: capture before you trust the headers

Where you have an authorized capture position, observe the negotiation and the file traffic directly (tcpdump/mitmproxy). Two things only a capture shows: whether sensitive file bytes cross a segment in cleartext (no SMB3 encryption — a deployment finding no scanner asserts), and whether a host silently accepts a dialect/signing **downgrade** mid-negotiation. Passive observation also corroborates the active findings without sending another probe.

---

## Phase 5 — Relay-precondition inventory and the legacy one-liner

### 5.1 The signing inventory IS the finding

NetExec generates the relay-precondition list directly — this is the deliverable, framed as an audit, not an attack:

```bash
# write a file of every host NOT requiring SMB signing across the range
nxc smb 198.51.100.0/24 --gen-relay-list relay_list.txt
#  -> relay_list.txt contains one IP per line: the signing:False hosts
```

(The same `--gen-relay-list` flag existed on the CrackMapExec predecessor; the NetExec wiki documents it under "Enumerate Hosts with SMB Signing Not Required.") Each line is a reportable misconfiguration: the host would accept a relayed authentication. **Stop at the inventory.** The actual relay (`ntlmrelayx.py -tf relay_list.txt -smb2support`) requires coercing a victim to authenticate to you — a multi-host, intrusive action outside a bounty's single-target, low-volume discipline and usually outside scope. Report "SMB signing not required on N hosts (list attached); these are relay targets if an attacker can coerce authentication" with the per-host evidence from `smb2-security-mode` ("enabled but not required").

Why this framing is the correct one: the nmap `smb-security-mode` doc states it plainly — *"If message signing isn't required, the server is vulnerable to man-in-the-middle attacks or SMB-relay attacks."* The vulnerability is the posture; the relay is the exploitation of it. On a bounty you prove the posture.

### 5.2 Cross-reference the domain context

A `signing:False` member server matters far more inside an AD domain (coercion primitives like the printer/EFS RPC lanes can drive authentications at it) than a standalone box. Note the domain membership from `smb-os-discovery`/the probe line and hand the relay list to the ntlm/relay unit rather than acting on it here.

### 5.3 EternalBlue-era legacy (one line)

If `NT LM 0.12`/`(SMBv1:True)` is present on a pre-2017 build, flag the **MS17-010 (EternalBlue)** generation as an era/hygiene finding and confirm against [[smb-windows-native]] — never treat an NSE `smb-vuln-ms17-010` hit as a reportable RCE without build confirmation, and never fire the exploit on a bounty.

---

## 6. Reporting & evidence standards

The report is the product. Structure every SMB finding the same way:

1. **Title — impact and exact locus.** "Anonymous SMB session reads `\\198.51.100.10\Backups`, exposing `web.config` with plaintext DB credentials" beats "SMB misconfiguration."
2. **Preconditions.** The credential rung used (null / guest / a specific low-priv account), and nothing more privileged than you actually needed.
3. **PoC.** Copy-pasteable `nxc`/`smbmap`/`smbclient` one-liners, one command per step, with the expected vs. actual output (the `Permissions` tier, the `NT_STATUS_*` code, the `signing:False` line).
4. **Evidence.** The permission-map table, the spider inventory (filenames, not bulk contents), and only the *minimum* file content that proves impact — redacted. For a WRITE finding, the upload→list→delete marker sequence. Clean up every marker and test artifact; note the cleanup in the report.
5. **Remediation — protocol-specific.** Point at the exact control: require SMB signing (GPO / `Set-SmbServerConfiguration -RequireSecuritySignature $true`); disable the Guest account / block insecure guest logons; remove SMBv1; fix the share ACL to the least-privilege tier; enable SMB3 encryption on sensitive shares.

Severity rules of thumb: writable code/deployment share or cross-account read of secrets — High–Critical; guest/anonymous read of non-public data — Medium–High; signing-not-required inventory — Medium (High in an AD context); SMBv1 enabled / null-session info disclosure / error oracle — Low–Medium. Map to the program's rubric.

### Falsifiable coverage closeout

| Claim you tried to prove | Rejected (secure) if |
|---|---|
| An unauthenticated client reaches share data | null and guest sessions both denied on every share |
| A low-priv account reads/writes another tenant's share | every share's ACL matches its intended tier (3.1) |
| The host accepts a relayed authentication | `smb2-security-mode` = "enabled and required" on every host |
| Account existence leaks from session setup | valid and invalid usernames return identical `NT_STATUS` |
| Sensitive data crosses the segment in cleartext | SMB3 encryption on, confirmed by capture (4.4) |
| A legacy dialect/RCE class is reachable | `NT LM 0.12` absent; build patched past MS17-010 |

---

## 7. CTF notes

SMB is a top-three CTF foothold surface; the enumeration order is nearly always the same:

- **Always start with the free sweep:** `nxc smb <box>`, then `smbclient -L //<box> -N` and `smbmap -H <box>` with no creds. Null/guest-readable shares are the intended path on a huge fraction of boxes.
- **`-N` first, `guest%` second.** Many CTF shares are anonymous-readable; where null fails, try `smbclient //<box>/share -U 'guest%'`.
- **Read *everything* readable.** CTF flags and credential leaks hide in `README`/`notes.txt`/`backup`/`.bak` files and in `smb.conf` itself. `nxc -M spider_plus` then grab the interesting files.
- **Writable share = foothold.** A writable `profiles$`/scripts/web-root share is usually the exploitation step — drop a payload where something executes it (often paired with a cron/scheduled-task or a web shell). Writable `NETLOGON`/`SYSVOL` on an AD box is a logon-script RCE.
- **`enum4linux-ng -A` for the full picture**, then `-R` for RID cycling to recover usernames to spray (CTF lockout policies are usually absent, so spraying is fair there — unlike a real engagement).
- **SMBv1-only legacy boxes** are the EternalBlue/MS08-067 teaching boxes; force `client min protocol=NT1` to enumerate them, then the version dates the exploit.
- **Password reuse is the pivot:** credentials found in one readable share almost always validate elsewhere — feed them straight back into `nxc smb <range> -u … -p … --continue-on-success`.

---

## References

- NetExec wiki — SMB protocol (enumerate shares & access, RID brute, spidering, credential checks, password policy): https://www.netexec.wiki/smb-protocol
- NetExec wiki — Enumerate Hosts with SMB Signing Not Required (`--gen-relay-list`): https://www.netexec.wiki/smb-protocol/enumeration/smb-signing-not-required
- smbmap — README (flags, permission tiers, `-r`/`--depth`/`-A`/`-x`): https://github.com/ShawnDEvans/smbmap
- enum4linux-ng — README (`-A`, `-R`/`-r` RID cycling, `-oJ`/`-oY`): https://github.com/cddmp/enum4linux-ng
- Impacket — examples (`smbclient.py`, `smbserver.py`, `ntlmrelayx.py`): https://github.com/fortra/impacket/tree/master/examples
- nmap NSE — smb2-security-mode: https://nmap.org/nsedoc/scripts/smb2-security-mode.html
- nmap NSE — smb-security-mode: https://nmap.org/nsedoc/scripts/smb-security-mode.html
- nmap NSE — smb-protocols: https://nmap.org/nsedoc/scripts/smb-protocols.html
- nmap NSE — smb2-capabilities: https://nmap.org/nsedoc/scripts/smb2-capabilities.html
- nmap NSE — smb-os-discovery: https://nmap.org/nsedoc/scripts/smb-os-discovery.html
- Microsoft Learn — SMB signing (24H2 / Server 2025 defaults): https://learn.microsoft.com/en-us/windows-server/storage/file-server/smb-signing
- Microsoft Learn — Insecure guest logons disabled by default (SMB2/SMB3): https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/guest-access-in-smb2-is-disabled-by-default
- Microsoft Tech Community — SMB signing required by default (Insider/Server 2025): https://techcommunity.microsoft.com/t5/storage-at-microsoft/smb-signing-by-default-is-coming/ba-p/3831704

*Researched and written 2026-11-19; commands verified against NetExec wiki, smbmap/enum4linux-ng READMEs, Impacket examples, nmap NSE docs, and Microsoft Learn (SMB signing; insecure guest logons) on 2026-11-19; companion overview [[smb]]; drafted with model claude-opus-4-8.*
