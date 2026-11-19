---
name: "backorifice"
categories:
  - blackarch-windows
version: "1.0"
homepage: "http://www.cultdeadcow.com/tools/bo.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# backorifice

> Cult of the Dead Cow's original 1998 Back Orifice — the encrypted-UDP remote administration client/server pair for Windows 95/98 (this is BO v1.20, not its 1999 successor BO2K).

## What it's for

Back Orifice v1.20 (cDc, August 1998) is a client/server remote administration system whose server self-installs on Windows 95/98 and listens for encrypted UDP packets — by default on port 31337 with no password, installing itself as " .exe". The package contains the self-installing server (boserve.exe), a text client (boclient.exe) and a GUI client (bogui.exe), the boconfig.exe utility for pre-setting the server's installed name, port, password and default plugin, and the freeze.exe/melt.exe compressor pair used with the file freeze/melt commands. The command set covers files and directories, hidden shares, the registry, processes, keylogging, screen and video capture, TCP file transfer and redirection of console applications to TCP ports, plus a plugin system. A historic malware-era tool — of interest for lab study and malware history rather than current operations. BlackArch ships the original 1998 archive (BO.TXT documentation included) under /usr/share/windows/backorifice/.

## Typical use cases

### Start the text client

```
BOCLIENT.EXE
```

Outcome: opens the interactive BO> console client that drives a Back Orifice server with encrypted UDP commands; `help` lists the command set and `help <command>` details one (ping, dir, view, keylog, appadd, capscreen, shareadd, regsetval, ...). The client can send from a chosen UDP port with its -p option, and its sweep command probes a subnet for responding servers by targeting an address form like 198.51.100.*.

### Configure a server copy before deployment

```
BOCONFIG.EXE
```

Outcome: runs the interactive configurator that rewrites a boserve.exe copy with the filename it will install itself as, the UDP port to listen on, the packet-encryption password and the default plugin to load — leaving it unconfigured yields the port 31337 / no password / " .exe" defaults.

### Read the bundled command reference

```
grep -A2 -i 'keylog\|appadd\|regsetval' /usr/share/windows/backorifice/BO.TXT
```

Outcome: pulls the packaged 1998 manual's entries for those commands out of BO.TXT — the archive's own documentation is the complete reference for all gui/text command pairs, the plugin API (PLUGIN.TXT) and known bugs of the release.

*No corpus evidence — derived from upstream documentation.*
