---
name: "u3-pwn"
categories:
  - blackarch-backdoor
version: "2.0"
homepage: "http://www.nullsecurity.net/tools/backdoor.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
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

# u3-pwn

> A tool designed to automate injecting executables to Sandisk smart usb devices with default U3 software install.

*No corpus evidence — derived from the packaged source (release tarball in the [nullsecuritynet tools collection](https://github.com/nullsecuritynet/tools) under `backdoor/u3-pwn`, which the frontmatter links). Description-only note — see below.*

## What it's for

U3-Pwn (Null Security team, 2013, author Zy0d0x) weaponized the era's SanDisk U3 "smart" USB sticks. Stock U3 sticks present a small read-only CD-ROM partition whose ISO auto-runs on plug-in; U3-Pwn backs up that factory ISO, then builds a replacement ISO with autorun behavior and injects a chosen executable — by default driving Metasploit's `msfconsole` to produce the payload, with helper resources like `hidden.vbs` and `wscript.exe` to launch it hidden, plus a bundled `shellcodeexec.exe`. The whole flow is a menu-driven Python 2.7 program (`u3-pwn.py`) that shells out to `u3-tool`, `mkisofs`/`genisoimage` and Metasploit; its interaction is entirely prompted (device name, options), so there is no flag-level CLI to document. It is purely historical today: U3 hardware was discontinued around 2009, and Windows has blocked autorun from USB by default for over a decade — the tool is useful only as a reference for CD-ROM-emulation USB attacks.
