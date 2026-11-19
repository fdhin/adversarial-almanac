---
name: "winfo"
categories:
  - blackarch-windows
version: "2.0"
homepage: "https://vidstromlabs.com/freetools/winfo/"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# winfo

> Null-session enumeration of Windows NT/2000/XP hosts: users, shares, sessions and password policy, anonymously.

## What it's for

Winfo (Arne Vidstrom, ntsecurity.nu, v2.0) is a small Windows command-line tool that opens a null session (anonymous SMB logon) against a remote Windows NT/2000/XP/2003 host and dumps what that connection reveals: user accounts (flagging the built-in Administrator and Guest even when renamed), workstation/interdomain/server trust accounts, shares including hidden ones (`C$`, `ADMIN$`), active sessions and logged-in users, plus password and lockout policy. It is the classic quick check for RestrictAnonymous misconfiguration — if Winfo returns full lists, the host leaks account data to anyone. On hardened hosts it returns little or errors. It targets legacy SMB stacks; modern Windows closes these holes by default, so treat it as a tool for old estates and lab ranges.

## Typical use cases

### Enumerate a host over a null session

One argument — the target — and everything the anonymous connection allows comes back.

```
winfo.exe 198.51.100.10
```

Outcome: prints sections headed `SYSTEM INFORMATION`, `DOMAIN INFORMATION`, `PASSWORD POLICY`/`LOCKOUT POLICY`, `USER ACCOUNTS`, trust accounts, `SESSIONS`, `LOGGED IN USERS` and `SHARES` (hidden shares included); an empty result or connection errors indicate null sessions are restricted on the target. Redirect with `> winfo.txt` to keep the output.

*No corpus evidence — derived from upstream documentation (Vidstrom Labs/ntsecurity.nu tool page) and published third-party usage transcripts showing the `winfo <ip>` argument form.*
