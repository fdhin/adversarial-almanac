---
name: "intercepter-ng"
categories:
  - blackarch-windows
version: "1.0"
homepage: "http://sniff.su/download.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "filo997/intercepter-ng"
github-stars: 3
github-pushed: "2016-01-02"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# intercepter-ng

> Multi-protocol Windows sniffer and man-in-the-middle toolkit by ares, with console and Android editions.

## What it's for

Intercepter-NG (official site sniff.su, © ares, 2006–2014) is a GUI network toolkit for Windows whose main purpose is recovering interesting data from the network stream: passwords and hashes for FTP, HTTP, IMAP, POP3, SMTP, LDAP, VNC, MySQL, Oracle, NTLM, Kerberos and more, chat messages, and file reconstruction from HTTP/FTP/IMAP/POP3/SMTP/SMB traffic. Beyond passive sniffing it does network discovery (promiscuous, ARP, DHCP, gateway and port scanning), packet capture with offline post-capture analysis and RAW mode, and a range of MiTM attacks — ARP poisoning, DNS/NBNS/LLMNR spoofing, SSLStrip, WPAD abuse, SMB relay and SSH MiTM. It ships in three editions: the primary Windows GUI, a Console Edition (curses interface plus a command mode) for Windows/Linux/BSD/macOS/iOS/Android, and an Android app. It is GUI-first; reach for the Console Edition only on stripped-down hosts.

## Typical use cases

### Start the Windows GUI sniffer

```
Intercepter-NG.exe
```

Outcome: opens the GUI (the suite bundles the WinPcap DLLs `packet.dll` and `wpcap.dll`); pick a network adapter and start capture to watch recovered credentials, sessions and reconstructed files arrive in real time.

### Open the Console Edition interface

```
./intercepter
```

Outcome: starts the Console Edition in its curses interface (optimized for terminals 80x30 or larger), providing the same password/hash sniffing and file reconstruction in a terminal; run without arguments, this is the documented GUI mode.

### Show Console Edition command-mode help

```
./intercepter -h
```

Outcome: prints the console-mode usage — capture is selected by interface number or dump mode, with optional gateway and target arguments — instead of opening the curses interface. Console-mode ARP poisoning is unavailable in the Windows build of the Console Edition.

*No corpus evidence — derived from upstream documentation.*
