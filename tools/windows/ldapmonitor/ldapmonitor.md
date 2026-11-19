---
name: "ldapmonitor"
categories:
  - blackarch-windows
version: "1.4.r13.g6f9dfcb"
homepage: "https://github.com/p0dalirius/ldapmonitor"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: windows
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "p0dalirius/ldapmonitor"
github-stars: 950
github-pushed: "2025-10-30"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ldapmonitor

> Watch LDAP objects being created, changed and deleted in near real time — see each attack step land as it happens.

## What it's for

ldapmonitor (p0dalirius' LDAPmonitor) polls a domain controller with paged LDAP searches and prints a live feed of object creation, deletion and attribute changes, plus user logon events. During adversary simulation it answers "did my change actually take effect": privilege-escalation edits (e.g. the ACE written by ntlmrelayx's `--escalate-user`) and account lockouts appear the moment they happen; for administration it is a lightweight change monitor. The project ships Python, C# and PowerShell implementations — this package installs the Python one as the `ldapmonitor` command. It is read-only. Refresh rate is bounded by domain size (roughly a second per LDAP page of 1000 objects), so large domains cycle slower; raise `--page-size` or accept the lag.

## Typical use cases

### Live-monitor a domain with credentials

```
ldapmonitor -d example.com -u jdoe -p Password1 --dc-ip 198.51.100.10
```

Outcome: a continuously updating console feed of LDAP object creations, deletions, attribute modifications and user logons; leave it running while you trigger actions elsewhere and each event appears with the object and what changed. Interrupt with Ctrl-C.

### Monitor over LDAPS with a hash and a logfile

```
ldapmonitor -d example.com -u jdoe -H :5f4dcc3b5aa765d61d8327deb882cf99 --use-ldaps --dc-ip 198.51.100.10 -l ldapmon.log -t 2
```

Outcome: the same live feed over LDAPS, authenticated by NTLM hash instead of a password, sampled every 2 seconds and mirrored to ldapmon.log for later review. Add `--ignore-user-logon` to drop the noisy logon events and keep only object changes.

*No corpus evidence — derived from upstream documentation and source (`python/pyLDAPmonitor.py`, verified against the packaged BlackArch build).*
