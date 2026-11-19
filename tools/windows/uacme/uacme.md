---
name: "uacme"
categories:
  - blackarch-windows
version: "319.3f1885c"
homepage: "https://github.com/hfiref0x/UACME"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-08-02"
build-system: windows
meta-checked: "2026-11-09"
github-repo: "hfiref0x/UACME"
github-stars: 7851
github-pushed: "2026-07-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 24
gh-activity-checked: "2026-11-09"
---

# uacme

> UACME — a working catalogue and proof-of-concept launcher for Windows UAC bypass methods.

## What it's for

UACME (hfiref0x) documents and demonstrates dozens of ways to defeat Windows User Account Control: each numbered method is a distinct auto-elevation abuse (DLL hijack, CMSTPLUA, fodhelper-style registry abuse, and so on) with a table in the README stating the technique, affected Windows builds and the version it was fixed in. The compiled deliverables are `akagi32.exe`/`akagi64.exe`, which launch a chosen payload with elevated (high-integrity) rights without a UAC prompt — on hosts where the current user is already in the administrators group and UAC is the only barrier. Pick a method number that matches the target's Windows build from the README's methods table; methods "fixed" by Microsoft are removed from current releases (older branches keep them). This is a local privilege step from admin-group user to elevated process, not a remote exploit and not a way past being a non-admin.

## Typical use cases

### Pop an elevated command prompt via a chosen method

Validate that a bypass works on a given Windows build.

```
akagi64.exe 61
```

Outcome: if method 61 works on this build, an elevated `cmd.exe` opens (the default payload when no command is given); if the method fails or is fixed, the process exits with the error in its console output.

### Launch a specific tool elevated

Run an arbitrary executable with high integrity instead of the default cmd.

```
akagi64.exe 23 c:\tools\tool.exe
```

Outcome: `tool.exe` starts with administrator rights and no UAC consent prompt; verify with `whoami /groups` inside the launched process (the Mandatory Label shows High Mandatory Level).

*No corpus evidence — derived from upstream documentation (hfiref0x/UACME README usage and methods table).*
