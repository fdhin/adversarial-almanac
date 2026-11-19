---
name: "dbusmap"
categories:
  - blackarch-scanner
version: "16.6bb2831"
homepage: "https://github.com/taviso/dbusmap"
packaged-by: [blackarch]
blackarch-builddate: "2020-09-16"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "taviso/dbusmap"
github-stars: 80
github-pushed: "2019-11-22"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dbusmap

> An nmap for D-Bus: enumerates the services, methods and properties exposed on the local system bus and probes which ones your account may actually invoke.

*No corpus evidence — derived from upstream documentation (taviso/dbusmap README; the installed command is `dbus-map`).*

## What it's for

dbusmap, by Tavis Ormandy (command `dbus-map`), automates D-Bus attack-surface discovery on the local machine. Listing bus client names is easy; the laborious part is drilling down through each service's object-path hierarchy to enumerate the methods and properties it exports, and that is what dbus-map does — mapping every client to its owning PID, user and command line along the way. Beyond enumeration it probes: it can test each method and property for permission (invoking methods with invalid parameters to distinguish "access denied" from "bad arguments"), register a null PolicyKit agent so the authentication prompts its probing raises are auto-cancelled, and dump polkit actions filtered by whom they authorize. It reads the local system bus — it is local privilege-escalation recon, not a network scanner, and it deliberately cannot auto-authenticate.

## Typical use cases

### List bus clients and map them to processes

```
dbus-map
```

Outcome: a table of PID, user, well-known (or `:unique`) name and owning command line for every client on the system bus — the first pass over what is actually running behind those names. With probes enabled, a `!` after a name marks an unprotected name you are allowed to take over.

### Dump every exposed method and property

```
dbus-map --dump-methods
dbus-map --dump-methods --dump-properties
```

Outcome: each service's exported methods print as `m:<interface>.<method> <object path>` lines under its table row, and with `--dump-properties` its properties print with a `p:` prefix — the full call surface to mine for unauthenticated or weakly guarded functionality.

### Probe what your session may actually invoke

```
dbus-map --dump-methods --enable-probes --null-agent
```

Outcome: methods and properties are probed (invalid calls, properties re-set to their current values) and only the ones dbus-map believes your session can invoke survive the filter; `--null-agent` auto-cancels the polkit authentication prompts the probing generates. Investigate survivors with `dbus-send` or the standard `pkaction` tooling.

### List polkit actions and who they authorize

```
dbus-map --dump-actions=inactive=yes,any=yes
```

Outcome: prints PolicyKit actions in an Any/Inactive/Active permission table, filtered here to actions granted to remote (inactive) sessions or to anyone — exactly the actions a compromised unprivileged or SSH login could exercise without authentication.
