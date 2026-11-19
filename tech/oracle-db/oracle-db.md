---
technology: "Oracle DB / TNS"
kind: overview
audience: offensive-security
family: databases
layer: data store
language: C/Java (PL/SQL surface)
homepage: https://www.oracle.com/database/
github-repo: https://github.com/oracle/db-sample-schemas
provenance: "corpus trace = 1 writeup (an nmap service-probe output line naming the oracle-tns probe — no writeup attacks Oracle; see internal coverage ledger) — upstream-verified only (Oracle Security Alert CVE-2012-1675 'TNS Listener Poison', Oracle 19c docs: system-privilege accounts + password guidelines, odat wiki/README, nmap NSE oracle-sid-brute, Kali tnscmd10g, red-database-security) + supplement (2026-11-13) rewritten from HackTricks (src/network-services-pentesting/1521-1522-1529-pentesting-oracle-listener.md), CC BY-NC 4.0, accessed 2026-11-13 (oracle-tns-version/VSNNUM probe, ODAT Kali packaging, UTL_FILE + PREPROCESSOR two-layer gating, listener-vs-database credential split)"
---

# Oracle DB / TNS

> The database that anchors three decades of enterprise estates behind a single gate — the TNS listener on 1521 — whose unauthenticated protocol will tell you its version, its SID (with a wordlist), and, in the TNS-Poison era, let you register yourself as the cluster's database.

## What it is

Oracle Database is the archetypal commercial RDBMS (relational, PL/SQL stored-program language, C core with a Java layer). Clients never talk to the database process directly: the **TNS listener** (Transparent Network Substrate, Oracle Net) listens on **1521** (often a whole 1521–1560 range), speaks a binary, length-prefixed protocol, and brokers connections to named database instances identified by a **SID** or service name. You cannot log in without naming the right SID, so the attack sequence is always the same: version the listener → discover the SID → find credentials → land a SQL*Plus or ODAT session → escalate inside the database.

Two structural facts drive the attack surface. First, **the listener leaks by design**: `STATUS`/`VERSION` commands return version and instance detail to any unauthenticated network peer — which is why Oracle ships a dedicated "Oracle TNS Listener Security" documentation section, and why nmap's service scan probes the port with a probe literally named `oracle-tns` (the exact trace the field shows) and ships a dedicated `oracle-sid-brute` NSE script built on Alexander Kornbrust's SID wordlist. Second, **the ecosystem's history is credential-shaped**: a 1979-era demo account (`scott`/`tiger`) became the textbook default, whole families of schema accounts shipped with predictable passwords (`system`/`manager`, `dbsnmp`/`dbsnmp`, …), and Oracle's 19c documentation still warns that upgrades leave accounts on default passwords — queryable via the `DBA_USERS_WITH_DEFPWD` view.

The listener itself had its landmark bug: **CVE-2012-1675, the TNS Listener Poison attack** (disclosed April 2012 by Joxean Koret as a probably-13-years-old bug). A remote, unauthenticated attacker can remotely register a database instance with the listener; subsequent client connections are proxied to the attacker's host — a persistent MITM credential-harvesting position against every client of that listener, rated CVSS 7.5 by Oracle and explicitly "remotely exploitable without authentication".

## The anatomy that matters on an engagement

```
client (SQL*Plus / JDBC app)
   │  TNS connect packet: (ADDRESS=...(SERVICE_NAME|SID)=X)
   ▼
listener :1521 ──► brokers to database instance
   ├─ unauth commands: VERSION / STATUS / SERVICES   (★ recon without any creds)
   ├─ SID space: orcl, prod, XE, <company>*          (★ SID brute — the gate to login)
   ├─ CVE-2012-1675: remote instance registration    (★ TNS Poison MITM)
   └─ admin port 1521 + extproc/external procedures  (★ listener-side execution paths)
database instance (PL/SQL)
   ├─ default-cred accounts (scott/tiger era; DBA_USERS_WITH_DEFPWD)
   ├─ UTL_FILE / DBMS_JAVA / scheduler → OS files & commands as oracle user
   └─ HTTP endpoints: XDB :8080, APEX/PL/SQL gateway (mod_plsql, APEX_PUBLIC_USER)
```

Structural properties that drive findings:

1. **SID before login** — every auth attempt needs a valid SID; the SID wordlist (nmap `oracle-sid-brute`, ODAT `sidguesser`, metasploit `auxiliary/scanner/oracle/sid_brute`) is step one of every Oracle engagement.
2. **Unauthenticated listener protocol** — version/status/registration all happen before any credential check; the listener is an attack surface even when no login works.
3. **Inside the DB, execution is a library call away** — once a session exists, PL/SQL packages (`UTL_FILE`, `DBMS_SCHEDULER`, Java stored procedures) convert SQL privileges into OS file writes and command execution; ODAT's module list is essentially a catalog of these paths.

## Where you'll meet it

- **Legacy enterprise cores** — ERP/finance stacks (E-Business Suite, Forms/Reports apps) where an 11g-era listener still fronts the business's system of record; the hardest, oldest, most default-cred-prone surface on the internal network.
- **Oracle EBS / APEX web frontends** — the PL/SQL gateway (mod_plsql / ORDS) maps URLs to stored procedures; `APEX_PUBLIC_USER` and DAD misconfigurations are the web-side gate to the same database.
- **Cloud-adjacent lift-and-shift** — RAC clusters whose listeners were never patched for 2012-era issues because "it's internal".
- **Assessment periphery** — most modern red-team scopes meet Oracle as a port-1521 banner on an aging segment rather than the crown jewel itself; version + SID + default-cred triage is the proportionate first pass.

## Reading the listener's mind

- nmap identifies `1521/tcp open oracle-tns` (service probes carry the `oracle-tns` name — the field's only trace); real deployments often sweep 1521–1560.
- Legacy era context (verified against Oracle's own account docs): modern installs ship system accounts **locked with expired passwords** — DBCA creates only `SYS`, `SYSTEM`, `DBSNMP` unlocked-eligible; `scott/tiger` belongs to the 9i/10g demo era and survives only via upgrades, which Oracle flags through `DBA_USERS_WITH_DEFPWD`.
- tnscmd10g (Kali-packaged, from red-database-security) and ODAT both speak raw listener commands (`status`, `version`, `services`) — the pre-auth recon vocabulary.
- TNS Poison (CVE-2012-1675) is listener-side and unauthenticated: check by registering a fake instance (ODAT `tnspoison --test-module`) rather than assuming patch state from version strings.
- The 19c docs' table of system accounts (ANONYMOUS/XDB, APEX_050100, CTXSYS, DBSNMP, OUTLN, …) doubles as a username wordlist for `oracle_login`-style brute modules.

## Where to go next

- [[oracle-db-bug-bounty]] — full method: listener recon → SID brute → default/weak creds via ODAT → PL/SQL file/command execution → TNS Poison → XDB/APEX side doors → modern-surface summary.
