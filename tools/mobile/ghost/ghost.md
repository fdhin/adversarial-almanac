---
name: "ghost"
categories:
  - blackarch-mobile
version: "426.bf38c7e"
homepage: "https://github.com/EntySec/Ghost"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "EntySec/Ghost"
github-stars: 3437
github-pushed: "2025-11-01"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ghost

> Post-exploitation console for Android devices with an exposed ADB interface.

## What it's for

Ghost Framework (by EntySec) treats an Android device with the Android Debug
Bridge reachable over the network — typically TCP port 5555 with ADB
authorization effectively open — as the vulnerability itself, and provides a
console to connect to and operate such devices from one place. It is purely
post-exploitation: it exploits no bug in Android, it rides an administration
interface that was left exposed, giving shell-level access and file access on
the device. Despite the name it has nothing to do with the Ghost CMS publishing
platform.

## Typical use cases

### Open the framework console

Start the interactive console.

```
ghost
```

Outcome: prints the Ghost Framework banner and drops into the `(ghost)>`
prompt.

### Connect a device with exposed ADB

Attach to a device whose ADB port is reachable.

```
connect 198.51.100.10:5555
```

Outcome: the device joins the session (the port defaults to 5555 when
omitted); its numeric ID is printed, and `devices` lists every connected host
and port.

### Operate a connected device

Drop into one device's own console.

```
interact 0
```

Outcome: opens the per-device console for device `0`, from which its
post-exploitation commands run; `disconnect 0` drops the device again, `exit`
leaves the framework.

*No corpus evidence — derived from upstream documentation.*
