---
name: "impacket-mssqlclient"
categories:
  - corpus-windows
homepage: "https://github.com/fortra/impacket"
origin: "corpus"
github-repo: "fortra/impacket"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# impacket-mssqlclient

> Interactive TDS client for MSSQL with attack helpers built in: xp_cmdshell enabling, linked-server pivots, file upload.

## What it's for

Kali packaging of `mssqlclient.py` from fortra/impacket — a Python TDS
client (SSL supported) that drops into an interactive SQL shell whose
prompt shows the login and current database. It accepts SQL logins and
Windows authentication over TCP or a named pipe, plus impacket's usual
credential forms (password, `-hashes`, `-k` Kerberos). The shell has
dedicated attack commands beyond raw T-SQL: `enable_xp_cmdshell` /
`xp_cmdshell` for OS command execution, `enum_db` / `enum_users` /
`enum_links` for instance recon, `exec_as_login` / `use_link` for
privilege and linked-server pivots, and `upload` / `download` for file
transfer. It runs queries and commands as the authenticated principal —
it does not exploit anything by itself.

## Typical use cases

### Connect with SQL credentials and explore the instance

```
impacket-mssqlclient user:'Password1'@198.51.100.10 -p 1433
```

Outcome: opens the SQL shell (`Press help for extra shell commands`).
`enum_db`, `enum_users` and plain `SELECT ...` map databases, logins and
data; `-p` selects the port when the instance is not on the default
1433.

### Connect with Windows authentication

```
impacket-mssqlclient -windows-auth example.com/user:'Password1'@198.51.100.10
```

Outcome: authenticates as a Windows (domain) principal instead of a SQL
login — the right form when the instance is in Windows-auth mode or the
credential you hold is a domain account. SQL logins would fail there
with a login error.

### Enable xp_cmdshell and execute OS commands

```
SQL (sa@master)> enable_xp_cmdshell
SQL (sa@master)> xp_cmdshell whoami
```

Outcome: `enable_xp_cmdshell` flips `show advanced options` and
`xp_cmdshell` via `sp_configure`+`RECONFIGURE`; `xp_cmdshell <cmd>` then
runs an operating-system command under the SQL Server service account
and prints its output rows. Needs sysadmin on the instance — the classic
path from a privileged SQL credential to host-level execution.

### Connect with Kerberos (pass-the-ticket)

```
KRB5CCNAME=user.ccache impacket-mssqlclient -k -no-pass dc.example.com
```

Outcome: `-k` authenticates over TDS using the ticket exported in
`KRB5CCNAME` (e.g. from impacket-gettgt), with no password sent; target
the server by its FQDN hostname so Kerberos can build the SPN.
