---
name: "sweetpotato"
categories:
  - windows
homepage: "https://github.com/CCob/SweetPotato"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: dotnet
license: see repository
meta-checked: "2026-11-13"
github-repo: "CCob/SweetPotato"
github-match: "exact"
github-stars: 1848
gh-checked: "2026-11-13"
worth: 5.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# sweetpotato

> Local privilege escalation from Windows service accounts to SYSTEM via COM-based impersonation — RottenPotato, JuicyPotato, PrintSpoofer and EfsRpc collected in one binary.

## What it's for

SweetPotato (CCob/_EthicalChaos_) bundles the classic "potato" family of
SeImpersonatePrivilege abuses into a single C# executable: when you are
SYSTEM-adjacent but not SYSTEM — a service account (IIS, MSSQL, scheduled
task) holding `SeImpersonatePrivilege` — it triggers a local
authentication (COM activation, WinRM, the print spooler's named pipe, or
EfsRpc), intercepts it, and impersonates the arriving SYSTEM token to
launch your program. Choose the `-e` exploit mode matching what the host
allows: `DCOM` (CLSID-based, the JuicyPotato lineage), `WinRM`, `EfsRpc`
and `PrintSpoofer` (default). It is a local escalation primitive only —
it needs an account that already holds impersonate privileges, and the
modern mitigations (SERVICE SID hardening, patched spooler, Restricted
Admin WinRM) shrink its surface on current builds. Descendants like
GodPotato/GenericPotato cover newer Windows versions.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Escalate with the default PrintSpoofer mode

Standard path on a box where the print spooler runs.

```
SweetPotato.exe -p C:\Users\Public\payload.exe
```

Outcome: spawns a local SYSTEM token via the print-spooler named-pipe
trick and starts `payload.exe` under it (default `-p` is `cmd.exe` when
omitted); success shows the spooler handshake in the console output and
your callback (e.g. a shell check-in) arriving as `NT AUTHORITY\SYSTEM`.

### Escalate via EfsRpc

Fallback when the spooler is disabled or patched.

```
SweetPotato.exe -e EfsRpc -p C:\Users\Public\payload.exe
```

Outcome: abuses the Encrypting File System remote protocol to coerce a
SYSTEM authentication to the local COM listener; use where PrintSpoofer
fails — arguments to the launched program pass through `-a "..."`.

### Pick the DCOM CLSID explicitly (JuicyPotato mode)

Target a specific COM server on hosts with a known-good CLSID.

```
SweetPotato.exe -e DCOM -c 4991D34B-80A1-4291-83B6-3328366B9097 -p C:\Users\Public\payload.exe -l 6666
```

Outcome: activates the BITS CLSID (the default `-c`) on the local COM
server listening on `-l` (default 6666) and intercepts the resulting
SYSTEM token — swap the CLSID for one validated on that Windows build
when activation fails; `-e WinRM` is the fourth mode.

### Control the launch method

Choose how the program is started after the token is caught.

```
SweetPotato.exe -p powershell.exe -a "-enc <base64>" -m Thread
```

Outcome: `-m` selects the launch method — `Auto` (default), `User` or
`Thread` — with `-a` supplying the argument string; useful when the
default method's process-launch path is watched.
