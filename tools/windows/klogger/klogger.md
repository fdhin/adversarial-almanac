---
name: "klogger"
categories:
  - blackarch-windows
version: "1.0"
homepage: "http://ntsecurity.nu/toolbox/klogger/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-15"
build-system: windows
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# klogger

> Flagless Windows keystroke logger that writes everything typed to klogger.txt.

## What it's for

KLogger (v1.0, Arne Vidstrom, 2000) is a minimal keystroke logger for the NT-series of Windows (NT 4.0 through the Vista era). It takes no options and installs nothing: once started it records all keystrokes on the interactive desktop to `klogger.txt` in the directory it was started from, until its process is killed or the machine rebooted. It cannot capture the Windows logon or workstation-unlock password, and running two copies at once logs every key twice. Antivirus products routinely flag it as a hacking tool because of what it is, not because of any extra payload.

## Typical use cases

### Record keystrokes to a log file

```
klogger.exe
```

Outcome: starts logging silently — every keystroke typed on the desktop is appended to `klogger.txt` in the working directory. Stop it by killing the process in Task Manager (there is no stop command), then read the log with `type klogger.txt`.

*No corpus evidence — derived from upstream documentation.*
