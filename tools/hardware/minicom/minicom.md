---
name: "minicom"
categories:
  - hardware
version: "2.11.1-3"
homepage: "https://salsa.debian.org/minicom-team/minicom"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-06-01"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Distrotech/minicom"
github-stars: 68
github-pushed: "2023-10-05"
github-archived: false
github-issues: 13
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# minicom

> The classic menu-driven serial communication program — a full-screen terminal for consoles over RS-232 and USB-serial devices.

## What it's for

Talking to devices that speak over a serial line: the console port of switches, routers, servers and embedded boards, modem-style devices, and anything exposing a UART through a USB adapter. Minicom wraps a serial port in a terminal emulator with scrollback, capture, line editing, multiple dialing-directory profiles, ZMODEM/XMODEM/YMODEM file transfers and a script language for automating logins — which is why it remains the go-to when a device's manual says "connect with 9600 8N1". Configuration lives in menus (Ctrl-A Z is the help screen; Ctrl-A O opens settings) rather than in flags, and per-profile defaults are stored per user. It is a terminal, not an analyzer: for scripted or bulk capture prefer redirecting its log or using dedicated tooling.

## Typical use cases

### Attach to a device console

```
minicom -D /dev/ttyUSB0 -b 115200
```

Outcome: minicom opens `/dev/ttyUSB0` at 115200 8N1, runs its modem initialisation (skip with `-o` when the device is not a modem), and shows a full-screen session: typed characters go to the device, its output scrolls in the window. Ctrl-A Z lists all control commands, Ctrl-A X exits, Ctrl-A A toggles line-by-line "add carriage return" for devices expecting CRLF.

### Configure the connection through the setup menu

```
minicom -s
```

Outcome: opens the setup menu directly (as root this edits the system-wide default profile) — serial device, baud rate, parity, hardware/software flow control, and modem dialling settings. Save as a named configuration for the device; afterwards `minicom` alone reuses it. This is the path when a port needs flow control changed or a non-default speed made persistent.

### Capture a session to a file

Inside a running session: Ctrl-A L, then give a filename.

Outcome: everything received from the device is appended to the capture file while the session continues — the standard way to keep a boot log or an installer transcript from a serial console. Stop capture from the same menu entry; the file is plain text ready to grep.

### Automate a login over serial

```
minicom -S login.expect -D /dev/ttyUSB0 -b 9600
```

Outcome: minicom runs the named script at startup (expect-style `send`/`expect` sequences from its runscript language), typing the username and password when the device presents its login prompt — useful for recurring bench work against appliance consoles.

*No corpus evidence — derived from upstream documentation.*
