---
name: "brutus"
categories:
  - blackarch-windows
version: "2"
homepage: "http://www.hoobie.net/brutus/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# brutus

> Hoobie's classic Windows GUI remote password cracker (version AET2) — the late-'90s point-and-click tool for HTTP, POP3, FTP, SMB and Telnet password testing.

## What it's for

Brutus (hoobie.net, first made public in October 1998, current release AET2) is a Windows 9x/NT/2000 GUI application for remote password testing, originally written to check routers and similar devices for default and common passwords. Its multi-stage authentication engine covers HTTP (Basic Authentication and HTML Form/CGI), POP3, FTP, SMB and Telnet out of the box, with further authentication types (IMAP, NNTP, NetBus and others) importable as BAD files that users create and share. Around the engine sit up to 60 simultaneous target connections, no-username/single-username/multiple-username modes, password-list, combo (user/password) list and configurable brute-force modes, load-and-resume position, SOCKS proxy support for all types, HTML form interpretation, and user/password list generation and manipulation. It is entirely dialog-driven — there is no command-line interface, so it takes no scriptable examples — and development ended in the early 2000s; for the same job from a modern shell, console crackers such as [[hydra]] cover its ground. BlackArch ships the original brutus-aet2.zip contents under /usr/share/windows/brutus/.

*No corpus evidence — derived from upstream documentation.*
