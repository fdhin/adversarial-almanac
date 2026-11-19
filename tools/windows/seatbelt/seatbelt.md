---
name: "seatbelt"
categories:
  - windows
homepage: "https://github.com/GhostPack/Seatbelt"
origin: "pentest-book"
recipe: "upstream"
build-system: windows
meta-checked: "2026-11-13"
github-repo: "GhostPack/Seatbelt"
github-stars: 4762
github-match: "exact"
gh-checked: "2026-11-13"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "BSD-3-Clause"
---

# seatbelt

> C# host-reconnaissance binary: one executable, hundreds of read-only Windows security checks in grouped sweeps.

## What it's for

Seatbelt (GhostPack, by harmj0y) is a single C#/.NET binary that
enumerates security-relevant state on a Windows host: AV/EDR presence
(`AntiVirus`, `AMSIProviders`), LSA/NTLM/UAC and RDP configuration,
services, autoruns and scheduled tasks, patch level, and saved
credential material (cloud configs, RDP/PuTTY sessions, browser and
Chromium presence, PowerShell history, DPAPI master keys). Checks are
grouped (`-group=all|system|user|misc|chromium|slack|remote`) or
invoked individually by name, and applicable checks can be run
remotely over WMI. It is situational awareness for the first minutes
of an AD engagement — it only reads state and exploits nothing, and it
is not a credential dumper for LSASS. Output is operator-facing text,
so use `-outputfile` to capture results. The project is in
maintenance mode but remains the canonical Windows host-recon tool; no
official binaries are released — compile it yourself (see
[[sharpcollection]] for nightly community builds, and [[rubeus]] for
the sibling Kerberos toolkit).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Run every check

Get the broadest first-look at a host.

```
Seatbelt.exe -group=all
```

Outcome: executes every command group and prints sectioned findings
(AV products, LSA settings, credential artifacts, hotfixes, ...);
combine with `-full` to disable per-command result filtering.

### Survey system posture

Focus on OS configuration and defensive-tool signals.

```
Seatbelt.exe -group=system
```

Outcome: runs the ~45 system-side checks (`AMSIProviders`,
`AntiVirus`, `AppLocker`, `LAPS`, `LSASettings`, `NTLMSettings`,
`Hotfixes`, ...) and prints what a defender would care about —
patching, logging policy and EDR footprint.

### Run a single check unfiltered

Pull one artifact in full detail.

```
Seatbelt.exe PowerShellHistory -full
```

Outcome: runs only the named check (`AntiVirus`, `LAPS`,
`CloudCredentials`, ... all work the same way) and, with `-full`,
returns complete results instead of the default filtered top hits.

### Capture results quietly to a file

Make output machine-capturable.

```
Seatbelt.exe -group=user -q -outputfile="C:\Temp\seatbelt-user.json"
```

Outcome: runs the user-side checks in quiet mode (`-q` suppresses the
banner and status noise) and writes results to the given file —
`.json` produces structured output, `.txt` plain text.

### Enumerate a remote host

Extend coverage to hosts you have credentials for.

```
Seatbelt.exe -group=remote -computername=FILESRV.target.example.com -username=TARGET\user -password=Password1
```

Outcome: runs the WMI-executable checks against the remote machine
with the supplied local/domain credentials, covering both the
remote-specific group and applicable checks from other groups.

### Skip noisy checks in a full sweep

Trim the sweep without listing every wanted check.

```
Seatbelt.exe -group=all -AuditPolicies
```

Outcome: runs the full sweep EXCEPT the named command(s) — pass
exclusions as extra arguments to skip slow or noisy checks while
keeping the rest.
