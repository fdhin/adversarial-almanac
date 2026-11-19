---
technology: "MySQL"
kind: overview
audience: offensive-security
family: databases
unit-shape: "area (multi-implementation router)"
layer: data store
language: C/C++
homepage: https://www.mysql.com
github-repo: https://github.com/mysql/mysql-server
provenance: "corpus-grounded (84 writeups, command-context gated) + upstream-verified (MySQL/MariaDB docs, advisories)"
---

# MySQL (protocol family: Oracle MySQL, MariaDB, Percona)

> One wire protocol, three forks with divergent defaults — on Linux targets "mysql" almost always means **MariaDB**, and the fork you are actually facing decides whether `secure_file_priv`, passwordless root, and the auth-bypass CVEs apply.

## What it is

MySQL is the classic client/server RDBMS: a `mysqld` daemon speaking a challenge-response wire protocol on **3306/tcp** (plus a local Unix socket at `/var/lib/mysql/mysql.sock`), fronted by the `mysql` CLI. Oracle owns upstream MySQL; MariaDB is the 2009 Monty Widenius fork that ships as the *default* `mysql` package on Debian/Ubuntu and therefore dominates Linux targets you will actually meet on engagements; Percona Server is a third branch common in performance-tuned shops. The three share the protocol and SQL dialect closely enough that every client and every injector treats them as one thing — but their security defaults have diverged hard since MySQL 5.7/8.0 (file-write sandboxing, auth plugins, CVE lineage), which is the single most operationally useful fact about this unit.

For the attacker the design's core property is **blending of data plane and host plane**: SQL users with `FILE` privilege read and write server-host files (`load_file()`, `INTO OUTFILE`), the server can *pull* files off clients (`LOAD DATA LOCAL INFILE`), and shared libraries loaded as "user defined functions" execute as the daemon user. A foothold in the SQL layer is a foothold on the host, and every credential in a web config is a shot at both.

## Dispatch: identify the implementation first

Read the version string before anything else — it comes free in the nmap banner, the `mysql` handshake error, an SQLi `@@version`, or sqlmap's fingerprint log:

| Fingerprint | Implementation | Operator consequence |
|---|---|---|
| `10.x-MariaDB`, `11.x-MariaDB`, prompt `MariaDB [(db)]>` | MariaDB | `secure_file_priv` **empty by default** → `load_file()`/`INTO OUTFILE` anywhere the daemon can write; root via `unix_socket` on modern/deb installs; native-password hashes. See [[mysql-mariadb]]. |
| `8.0.x-MySQL`, `5.7.x-MySQL` | Oracle MySQL | `secure_file_priv` defaults to `/var/lib/mysql-files` (Linux packages) or `NULL` — file reads via SQLi mostly dead; `caching_sha2_password` since 8.0.4; different CVE lineage. |
| `5.5.5-10.x-MariaDB` (the `5.5.5-` prefix) | MariaDB spoofing old version handshake | Don't be fooled — the leading `5.5.5-` is MariaDB's MySQL-compat encoding. |
| `Percona Server`, `-cll-lve`, `-log` suffixes | Percona / cPanel builds | Treated as MySQL-line for CVEs; common on shared hosting. |
| `3306/tcp open mysql` with no version | either | Grab the banner yourself (`mysql -h 198.51.100.x` or the handshake bytes); version-gate your CVE attempts. |

Version strings embed the packaging OS (`-0ubuntu0.22.04`, `+maria~bionic`, `-deb12`), which tells you the plugin directory layout, the auth default, and which distro CVE patches apply. MariaDB version numbers jumped 5.5 → 10.x (never 6/7/8/9) → 11.x; **MariaDB 10.x is NOT MySQL 8.x** — never carry MySQL-8 CVEs onto a MariaDB target or vice versa without checking both advisories.

## The protocol-common surface (both forks)

These attack classes work (or fail) the same on both implementations; the [[mysql-bug-bounty]] methodology file is the executable version of this list.

1. **Credential pivoting.** Web apps store working DB creds in `wp-config.php`, `.env`, `config.php`, `~/my.cnf`, `jdbc:mysql://`/`jdbc:mariadb://` URLs. On classic LAMP boxes those creds are reused for SSH or admin panels more often than they are unique. The `mysql` CLI shape is always the same: `mysql -u <user> -p'<pass>' -h <host> [dbname]`.
2. **Authentication attacks.** Default/no-password root, password reuse, brute force, and the memcmp bug family — CVE-2012-2122 (MySQL 5.1.x<5.1.63, 5.5.x<5.5.24, 5.6.x<5.6.6; MariaDB 5.1.x<5.1.62, 5.2.x<5.2.12, 5.3.x<5.3.6, 5.5.x<5.5.23) accepts *any* password roughly 1-in-256 tries on distro builds compiled with SSE — loop the client a few hundred times.
3. **Server-side file read.** `UNION SELECT load_file('/etc/passwd')` — gated by `FILE` privilege and `secure_file_priv`. MariaDB's empty default makes this a reliable SQLi impact; MySQL ≥5.7 usually jails it.
4. **Server-side file write.** `INTO OUTFILE`/`INTO DUMPFILE` a PHP webshell into a web root (Windows targets: `'C:\\inetpub\\wwwroot\\shell.php'`), or a `.so` into the plugin dir. Needs `secure_file_priv` to permit the path and the daemon user to own the write; `/var/lib/mysql` is always writable by the daemon.
5. **Client-side file theft (rogue server).** The server *initiates* `LOAD DATA LOCAL INFILE` transfers — a malicious/patched server (rogue-mysql-server, MySQL::init utilities) can request any file the connecting client can read, and any captured native-password handshake becomes a crackable `$mysqlna$` hash. Every web app that connects to a user-controllable MySQL host is exposed.
6. **UDF command execution.** With SQL root and a writable plugin dir, `CREATE FUNCTION ... SONAME` on a compiled `raptor_udf2.so` gives OS command execution as the mysql user — and finding rows in `mysql.func` on a compromised host means *someone already did this* (backdoor/persistence check).
7. **Post-shell forensics.** `/var/lib/mysql` (data), `/etc/mysql/` (config tree, creds in `debian.cnf`), `~/.mysql_history`, and dumpable `mysqldump` scripts in cron tell you what the box actually serves.

## Where you'll meet it

- **LAMP/WordPress stacks** — the dominant context; creds in `wp-config.php` → console access → password reuse (the field's most frequent full chain).
- **Adminer / phpMyAdmin exposed** — a server-side MySQL *client* with a UI; SSRF into internal databases and `LOAD DATA LOCAL` file reads against the web host.
- **Docker-compose webservices** — `MYSQL_ROOT_PASSWORD=` in env files and compose configs, root bound to `0.0.0.0` inside the bridge network; reach it from any container compromise.
- **Internal-only 3306** — usually found after a foothold; treat as credential goldmine for the apps it backs.
- **Application backends** (Mirth, Cacti, Grafana, TeamPass, OpenCATS...) — each stores its DB creds in a well-known config path; knowing the app gives you the connection string.

## Reading the daemon's mind

- `@@version` embeds fork + distro → dispatch table above.
- `SHOW VARIABLES LIKE 'secure_file_priv'` → empty = filesystem is in scope; `NULL` = file ops dead.
- `SHOW VARIABLES LIKE '%plugin%'` → where a UDF `.so` must land (`/usr/lib/mysql/plugin/` on MySQL, `/usr/lib/.../mariadb19/plugin/` style paths on MariaDB debs).
- `SELECT user,host,plugin FROM mysql.user` → `unix_socket`/`auth_socket` entries mean passwordless root *from the local OS root*; `mysql_native_password` rows have crackable 20-byte double-SHA1 hashes.
- `SELECT * FROM mysql.func` → non-empty on a box you didn't just own = prior attacker persistence.

## Where to go next

- [[mysql-bug-bounty]] — the full phased hunt: fingerprint → auth attacks → post-auth enum → file R/W → rogue server → UDF → credential pivot.
- [[mysql-mariadb]] — the MariaDB implementation page: defaults, config layout, offline datadir attacks, fork-specific CVE notes.
