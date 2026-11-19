---
name: "kautilya"
categories:
  - blackarch-hardware
version: "52.1c9d5b0"
homepage: "https://github.com/samratashok/Kautilya/releases"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "samratashok/Kautilya"
github-stars: 872
github-pushed: "2017-08-04"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kautilya

> Generates Human Interface Device attack payloads for Teensy boards — an interactive menu that turns a USB "keyboard" microcontroller into a payload dropper for penetration tests.

## What it's for

Turning a Teensy (++ 2.0 / 3.0) programmable USB device into an attack tool: because the board enumerates as a keyboard/mouse, payloads it types execute with the logged-in user's rights, bypassing most network controls. Kautilya is the payload generator: pick a target OS and category (Gather, Execute, Backdoor, Escalate, Manage, Drop Files...) from its menus and it writes a `.pde`/Arduino sketch pre-filled with keystroke sequences — hashdump-and-exfiltrate, keylogging, reverse shells, DNS-TXT backdoors, adding an admin user, enabling RDP, dropping malicious shortcuts — which you flash to the Teensy with the Arduino IDE/Teensy loader. It generates source code, not exploits; the same sketches power any USB-HID rubber-ducky-style device after minimal editing. Development stopped years ago, so PowerShell snippets may need updating for current Windows, and HID attacks only work against an unlocked session.

## Typical use cases

### Generate a payload interactively

```
kautilya
```

Outcome: the menu starts (OS choice, then categories like Gather, Execute, Backdoor, Escalate, Manage, Drop Files), you walk the prompts (attacker IP, port, timing options), and it writes a ready-to-flash sketch file into its output directory. On a source checkout the same thing runs as `ruby kautilya.rb`. The generated `.pde` is then compiled and flashed to the Teensy with the Arduino IDE.

### Create a keylogger-and-exfiltrate payload for Windows

Choose Windows → Gather → Keylog and Exfiltrate and supply the upload endpoint.

Outcome: the generated sketch types out a PowerShell staging sequence that installs a keylogger and ships captured keystrokes to the specified host whenever the target has network access — the canonical "leave the device plugged in for a minute" payload.

### Backdoor a target's accessibility tools

Choose Windows → Backdoor → Sethc and Utilman backdoor.

Outcome: the sketch rebinds the accessibility binaries (sticky keys / utilman) to cmd.exe via registry commands typed at interactive speed — leaving a trivial local privilege-escalation trap on the machine after the device is removed. Verify generated commands against current Windows versions before use.

*No corpus evidence — derived from upstream documentation.*
