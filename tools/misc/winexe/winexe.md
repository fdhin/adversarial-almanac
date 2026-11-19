---
name: "winexe"
categories:
  - blackarch-misc
version: "1.00"
homepage: "http://sourceforge.net/projects/winexe/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2015-09-06"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "skalkoto/winexe"
github-stars: 72
github-pushed: "2018-05-06"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# winexe

> Executes commands on a remote Windows host over SMB from a Unix-like system, in the manner of PsExec.

## What it's for

winexe runs a command on a remote Windows machine from Linux. Built on Samba 4
libraries, it authenticates over SMB, installs a small service binary on the
target (32- or 64-bit, chosen automatically) and uses that service to execute
the command, streaming its output back — the same pattern as PsExec on
Windows. An interactive `cmd.exe` session is supported, and the service can
run the command as the login user, as SYSTEM, or under another account via
--runas. Note that the service stays installed on the target by default;
use --uninstall to remove it after a run, and that credentials passed with
-U travel in cleartext.

## Typical use cases

### Run a single command on a remote host

```
winexe -U 'DOMAIN\user%Password1' //198.51.100.10 'cmd /c whoami'
```

Outcome: authenticates over SMB, installs the winexe service on the target, executes the command as the login account and prints its output locally.

### Open an interactive remote shell

```
winexe --interactive=1 -U 'user%Password1' //198.51.100.10 cmd
```

Outcome: drops you into a live cmd.exe session on the target; run `exit` to end it. Interactive mode is not available on Windows Vista.

### Execute with SYSTEM privileges

```
winexe --system -U 'user%Password1' //198.51.100.10 'cmd /c net user'
```

Outcome: the service runs the command as the local SYSTEM account rather than the login user (this option wins over --runas).

### Keep credentials off the command line

```
winexe -A auth.txt //198.51.100.10 'hostname'
```

Outcome: reads the login credentials from the authentication file instead of the process list, then executes the command as usual.

### Remove the service after use

```
winexe --uninstall -U 'user%Password1' //198.51.100.10 'cmd /c ver'
```

Outcome: after the command completes, the winexe service is uninstalled from the remote machine, leaving no persistent artifact.

*No corpus evidence — derived from upstream documentation.*
