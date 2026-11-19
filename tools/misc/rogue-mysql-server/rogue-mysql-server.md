---
name: "rogue-mysql-server"
categories:
  - blackarch-misc
version: "2.78ebbfc"
homepage: "https://github.com/Gifts/Rogue-MySql-Server"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "Gifts/Rogue-MySql-Server"
github-stars: 472
github-pushed: "2013-09-15"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rogue-mysql-server

> Minimal fake MySQL server that abuses the LOAD DATA LOCAL INFILE client feature to make connecting MySQL clients read files from their own host.

## What it's for

Rogue MySql Server (by "Gifts", 2013) is a small Python 2 script that
 impersonates a MySQL server on port 3306. When a client connects, it
completes the handshake and then issues a malicious
`LOAD DATA LOCAL INFILE` response: because the MySQL protocol has the
*client* open and transmit the file, a vulnerable client can be tricked
into sending the contents of arbitrary local files (e.g. `~/.mysql`
history, `/etc/passwd`, web configs) to the fake server, where every
packet is logged to `mysql.log`. The target list of files to request is
hardcoded near the top of the script in the `filelist` tuple — you edit
the script, not the command line, to choose what to steal. It is the
canonical reference implementation of the rogue-MySQL-server attack
(MITM/CVE-2012-5611-era class); the original repo is dormant, but forks
such as jib1337/rogue-mysql-server extend it with argument parsing.

## Typical use cases

### Run the fake MySQL server and wait for a client

```
python2 rogue_mysql_server.py
```

Outcome: the script listens on 0.0.0.0:3306, completes the MySQL
handshake with any client that connects, and — after the client sends
its first query — responds with a forged `LOAD DATA LOCAL INFILE`
packet for each path in `filelist`. The client's file contents arrive
in the socket and are appended to `mysql.log` in the working directory
with timestamps. Stop with Ctrl-C and inspect the log for exfiltrated
data.

### Target a client file by editing the file list

```
filelist = (
    r'/var/www/html/wp-config.php',
    r'/home/user/.mysql_history',
)
```

Outcome: after editing the tuple at the top of `rogue_mysql_server.py`
and restarting, each connecting MySQL client is asked for those exact
paths instead of the default `c:\windows\win.ini`. Any file the client
process can read — database configs with credentials, shell histories,
source files — gets transferred to the attacker-controlled server and
captured in `mysql.log`.

Triggering requires the victim client to connect to you (a vulnerable
web app configured to your host, an SQL-injection-in-LOAD-DATA path, or
a MySQL client with local-infile enabled), which the note's
prerequisites prose makes explicit.

*No corpus evidence — derived from upstream documentation.*
