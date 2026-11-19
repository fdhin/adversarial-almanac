---
name: "smbbf"
categories:
  - blackarch-cracker
version: "0.9.1"
homepage: "http://packetstormsecurity.com/files/25381/smbbf.9.1.tar.gz.html"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-08"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# smbbf

> Windows NT/2000 SMB password brute-forcer from the classic "SMB Auditing Tool" suite by Patrik Karlsson.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Online password auditing of Windows NT and 2000 servers over SMB/NetBIOS.
Given only a target IP it enumerates the machine's users and tries each with a
blank password, the username in lowercase as password, and finally
`password`. With user and password files supplied it walks the full cartesian
product — around 1200 logins/sec against Windows 2000 (which answers failed
logins quickly due to a timeout bug), far slower against NT4. Password files
support magic entries (`%username%`, `lc %username%`, `uc %username%`,
`lc %hostname%`, `uc %hostname%`) that expand per target, and a blank line
means "try an empty password". The `-g` flag watches for account lockouts and
backs off to avoid disabling every account; lockout-aware auditing still
demands a password list shorter than the lockout threshold. The same package
ships the companion tools `smbdumpusers` (RID/SID user enumeration) and
`smbgetserverinfo`. This is 2003-era software: it speaks the NT4/Windows 2000
SMB dialects (`-P 0` NetBIOS mode, `-P 1` Windows 2000 native mode over port
445), not modern SMB3 signing/encryption.

## Typical use cases

### Quick default-password check

Find accounts using blank, username-equals-password, or `password` credentials.

```
smbbf -i 198.51.100.10
```

Outcome: retrieves the target's user list, tries each user with a blank
password, the lowercase username, and `password`, then prints every account
where a login succeeded.

### Audited run with user and password files

Test a controlled candidate set and keep a report.

```
smbbf -i 198.51.100.10 -s SERVER01 -w CORP -u users.txt -p pass.txt -r report.txt -g -P 1
```

Outcome: attempts every user/password pair (NetBIOS server name `SERVER01`,
workgroup `CORP`, Windows 2000 native mode), stops short of triggering
lockouts once `-g` observes one, and writes successful logins to
`report.txt`.

### Password files that expand per user and host

Cut the candidate list by deriving passwords from usernames and hostnames.

```
# pass.txt
%username%
uc %username%
lc %username%
lc %hostname%

smbbf -i 198.51.100.10 -s SERVER01 -u users.txt -p pass.txt
```

Outcome: for each user the magic entries expand to that username verbatim,
uppercased and lowercased, plus the server's name in lowercase, so one small
file covers many per-user guesses; a blank line in the file would additionally
try an empty password.
