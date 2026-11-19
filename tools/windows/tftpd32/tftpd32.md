---
name: "tftpd32"
categories:
  - windows-resources
version: "4.50-0kali3"
homepage: "https://tftpd32.jounin.net/tftpd32.html"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: windows
meta-checked: "2026-11-09"
github-repo: "PJO2/tftpd64"
github-stars: 854
github-pushed: "2025-10-21"
github-archived: false
github-issues: 41
github-match: "search"
gh-checked: "2026-11-09"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tftpd32

> Free Windows TFTP server (plus DHCP, DNS, SNTP and Syslog) also shipped as 64-bit Tftpd64.

## What it's for

Tftpd32/Tftpd64 (Philippe Jounin) is a lightweight, IPv6-ready TFTP server and client for Windows, with optional DHCP, DNS, SNTP and Syslog servers built into the same GUI; Tftpd32 is the 32-bit build of the same application and a Windows-service edition exists. In lab and assessment work it is the quick way to move files to and from Windows hosts over UDP/69 TFTP — trivial transfers when SMB/HTTP are blocked and the target still has the built-in `tftp.exe` client. It is a GUI application, not a CLI: you set the served directory and options in its settings dialogs and watch transfers in the main window. It does not do encrypted transfers (TFTP has no authentication or encryption).

## Typical use cases

### Serve files to a Windows target over TFTP

Start the server, then pull a file from the target host using its native TFTP client.

```
tftpd64.exe
tftp -i 198.51.100.10 GET nc.exe
```

Outcome: the first command opens the GUI server listening on UDP/69 and serving its base directory (set it in Settings to the folder holding your tools); run the second line on the target Windows host to fetch `nc.exe` into its current directory — each transfer is logged in Tftpd64's main window with peer, filename and progress.

### Push a file from a Windows host

Use Tftpd64's built-in client tab to retrieve a file from the target side without opening a listener there.

```
tftpd64.exe
```

Outcome: in the Client tab, enter the target's address as the remote host running a TFTP server, choose Local for the transfer direction, and put/confirm the file — the transfer and any TFTP option negotiation (tsize, blocksize, timeout) is shown in the log pane.

*No corpus evidence — derived from upstream documentation (tftpd32.jounin.net homepage and PJO2/tftpd64 project pages).*
