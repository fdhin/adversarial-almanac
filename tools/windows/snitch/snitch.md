---
name: "snitch"
categories:
  - blackarch-windows
version: "1.2"
homepage: "http://ntsecurity.nu/toolbox/snitch/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2016-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# snitch

> Drag-and-drop asterisk revealer for Windows 9x/NT password fields.

## What it's for

Snitch (Arne Vidstrom, ntsecurity.nu, v1.2) addresses the forgotten-password-in-a-dialog problem on Windows 95/98/ME/NT 4: many programs of that era displayed a saved password as asterisks while keeping the plaintext readable in the edit control. You run the tool, press the left mouse button in its "start field", keep the button held and drag the pointer onto the target password field; if the program stores its password recoverably, Snitch shows the plaintext immediately. It works only on programs coded in a way that leaves the password accessible, and not at all on Windows 2000/XP, where the window mechanism changed — so today it is a legacy-recovery curiosity and a teaching example of how 9x-era password boxes worked rather than a practical password-recovery tool. GUI only, no command-line interface.

## Typical use cases

### Reveal a password hidden behind asterisks

```
snitch.exe
```

Outcome: opens the Snitch window with its "start field"; press and hold the left mouse button there, drag onto the target dialog's password field, and — if that program keeps the plaintext recoverable — Snitch displays the password in the clear.

*No corpus evidence — derived from upstream documentation.*
