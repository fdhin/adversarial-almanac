---
name: "fport"
categories:
  - blackarch-windows
version: "2.0"
homepage: "http://www.foundstone.com/us/resources/proddesc/fport.htm"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fport

> Maps every open TCP/IP and UDP port on a Windows host to the process that owns it.

## What it's for

fport, from Foundstone, reports all open TCP and UDP ports on a Windows machine and maps each one to its owning process — PID, process name and full executable path. It shows the same port list you would get from `netstat -an`, but with the owning application attached, answering the classic triage question on a suspect or unknown Windows box: what is that port listening for? It targets Windows NT4, 2000 and XP out of the box, predating the process-mapping flags that later shipped with netstat and PowerShell, which makes it the tool of record on legacy systems. It is purely local reconnaissance: it reads the system's port and process tables and connects to nothing remote. Foundstone was absorbed into McAfee and the original product page is no longer online; the distributed zip is the practical reference copy.

## Typical use cases

### List every open port with its owning process

```
fport
```

Outcome: a table of PID, process name, port, protocol and executable path for every open TCP and UDP port — e.g. `392  svchost  ->  135  TCP  C:\WINNT\system32\svchost.exe` — immediately showing which binary is behind any port in question.

### Sort the listing for easier scanning

```
fport /ap
```

Outcome: the same table sorted by application path, grouping every port a binary owns together (`/p` sorts by port, `/a` by application name, `/i` by PID — switches accept `/` or `-`).

*No corpus evidence — derived from upstream documentation.*
