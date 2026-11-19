---
name: "cutecom"
categories:
  - hardware
version: "0.51.0-1.1"
homepage: "https://gitlab.com/cutecom/cutecom"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur, nixpkgs]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "neundorf/CuteCom"
github-stars: 155
github-pushed: "2018-08-21"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cutecom

> A Qt graphical serial terminal for talking to devices over RS-232/USB-serial — a point-and-click counterpart to minicom or `screen /dev/ttyUSB0`.

## What it's for

Interactive work with serial consoles: routers, switches, embedded boards, modems, Arduino-class devices and anything else exposing a UART. You pick the device, baud rate, data bits, parity and stop bits from a settings panel instead of memorising flags, then get a scrolling output view with an input line, file sending, hex display and input/output logging. Session support keeps one configured profile per device so you can switch between several attached gadgets quickly. It is purely a terminal: it does not do protocol analysis, scripting or capture-file decoding — for that, use a proper serial analyzer. The historical GitHub repository (neundorf/CuteCom) is an archive; active development lives on the GitLab homepage in the frontmatter.

## Typical use cases

### Open a serial console to a device

```
cutecom
```

Outcome: the CuteCom window opens with its last-used settings. In the Settings panel set Device (e.g. `/dev/ttyUSB0`), Baud (e.g. 115200), 8 data bits, no parity, 1 stop bit, then click Open. Everything the device transmits appears in the output view, and the input line (plus the Send button / Enter) writes to the port. A boot loader or system console over UART is the typical target.

### Start with a saved device profile

```
cutecom -s switch-console
```

Outcome: CuteCom opens with the named session's device and port settings preloaded, so a recurring target (a lab board, a switch) is connected without re-entering parameters. Sessions are managed from the settings panel.

### Inspect and log binary serial traffic

Open the device as usual, then enable the hex display and logging from the panel.

Outcome: received bytes are shown both as text and hex (useful when a device emits non-printable protocol frames), and a session log file captures the whole exchange for later analysis. Files can also be pushed to the device (raw or line-by-line) from the send-file control.

*No corpus evidence — derived from upstream documentation.*
