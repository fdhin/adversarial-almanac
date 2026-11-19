---
name: "gpowned"
categories:
  - blackarch-windows
version: "25.5de0c77"
homepage: "https://github.com/X-C3LL/gpowned"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-19"
build-system: windows
license: custom:custom
meta-checked: "2026-11-09"
github-repo: "X-C3LL/gpowned"
github-stars: 138
github-pushed: "2026-07-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# gpowned

> Python toolkit to enumerate, back up, edit and link Active Directory Group Policy Objects over LDAP(S).

## What it's for

GPOwned (by X-C3LL, aka psyconauta) manipulates Group Policies from a
Linux attack host: it speaks to a domain controller over LDAP or LDAPS
using impacket and ldap3, with password, NT-hash, AES-key or Kerberos
authentication. Beyond enumeration it implements editing primitives that
turn write access on a single GPO into code execution or data movement on
every host that applies it — copying files into the GPO, creating
registry keys, changing service states, adding Immediate (scheduled) Task
elements or exfiltrating files — plus link/unlink and version-bump
actions. The author labels it a buggy proof of concept intended for lab
play; expect to adapt the code for real engagements. It is Linux-side
tooling: GPOwned edits the policy objects, the payloads take effect on
Windows hosts at next policy application.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Enumerate GPOs in a domain

Get an overview of Group Policy Objects and their targets.

```
python3 GPOwned.py -u user -p Password1 -d example.com -dc-ip 198.51.100.10 -gpcmachine -listgpo
```

Outcome: authenticates over LDAP and prints the domain's GPOs with their
identifiers — the `{GUID}` names needed by every later action.
`-gpcmachine` scopes to machine policies; `-gpcuser` scopes to user
policies.

### Back up a GPO before editing it

Preserve the original policy for restoration.

```
python3 GPOwned.py -u user -p Password1 -d example.com -dc-ip 198.51.100.10 -gpcmachine -backup /tmp/gpo-backup -name "{31B2F340-016D-11D2-945F-00C04FB984F9}"
```

Outcome: downloads the GPO's files from SYSVOL into the backup folder,
giving a copy to diff against or restore after your edits.

### Plant an Immediate Task in a GPO

Convert GPO write access into command execution on applying hosts.

```
python3 GPOwned.py -u user -p Password1 -d example.com -dc-ip 198.51.100.10 -gpcmachine -gpoimmtask -name "{31B2F340-016D-11D2-945F-00C04FB984F9}" -author "EXAMPLE\Administrator" -taskname "UpdateTask" -taskdescription "Policy maintenance" -taskargs "-Command Cleanup" -dstpath "C:\\Windows\\System32"
```

Outcome: edits the GPO's scheduled-tasks XML with a new Immediate Task; on
next Group Policy refresh, every host applying the GPO executes the task
under the configured author context.

### Bump a GPO version after manual edits

Force clients to see and apply modified policy content.

```
python3 GPOwned.py -u user -p Password1 -d example.com -dc-ip 198.51.100.10 -gpcmachine -gpoupdatever -name "{31B2F340-016D-11D2-945F-00C04FB984F9}"
```

Outcome: updates the version in both the GPT.INI file and the LDAP
object so domain hosts detect the change and re-apply the policy instead
of serving the cached copy.
