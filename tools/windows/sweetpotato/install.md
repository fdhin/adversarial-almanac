---
tool: "sweetpotato"
build-system: "dotnet"
upstream: "https://github.com/CCob/SweetPotato"
recipe: "upstream"
license: "see repository"
---

# Installing sweetpotato

> Local privilege escalation from Windows service accounts to SYSTEM via COM-based impersonation — the potato family collected in one binary.

Source: hacktricks feed; upstream [`CCob/SweetPotato`](https://github.com/CCob/SweetPotato).

## Prebuilt / releases

Check [upstream releases](https://github.com/CCob/SweetPotato/releases) for compiled binaries.

## Build from source (Windows)

```bash
git clone https://github.com/CCob/SweetPotato.git
```

Open the solution in Visual Studio and build Release (a .NET Framework
project). The built `SweetPotato.exe` is self-contained — copy it to the
target host with your `SeImpersonatePrivilege`-holding service account and
run in place.

## AV/EDR note

Compiled offensive .NET tools are signatured heavily; compile from source,
customize, or load through your own tooling as appropriate for the
engagement. On current Windows builds, prefer the EfsRpc mode when the
print spooler and DCOM surface are hardened — or one of the successor
tools (GodPotato, GenericPotato) for newer versions.

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[windows]] · [[sweetpotato]]
