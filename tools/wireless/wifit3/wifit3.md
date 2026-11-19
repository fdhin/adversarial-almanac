---
name: "wifit3"
categories:
  - wireless
version: "git (2026-11-07)"
homepage: "https://github.com/derv82/wifit3"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "derv82/wifit3"
github-stars: 3444
github-pushed: "2026-11-08"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 2997
gh-activity-checked: "2026-11-09"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 18
---

# wifit3

> Standalone USB Wi-Fi auditor from wifite's author: userland drivers, no aircrack-ng, runs on Linux, Windows and macOS.

## What it's for

wifit3 is derv82's successor to [[wifite]]/[[wifite2]] (see [[wifite2]] for that lineage): instead of wrapping system tools, it ships Python ports of Linux kernel Wi-Fi drivers and drives supported USB adapters directly over USB, so monitor mode and frame injection work in user space on Linux, Windows and macOS alike — no kernel drivers, no aircrack-ng, no NDIS. It scans 2.4/5 GHz (aggregating several adapters), fingerprints APs and clients, and runs WPA/WPA2 handshake capture (`.pcap`/`.hc22000` export), PMKID harvesting, an evil-twin WPA3-downgrade attack, the WPS suite (PixieDust in two modes, PushButton PSK extraction, resumable PIN brute-force) and pure-Python WEP attacks. The hard constraint: it only supports listed USB chipsets (AR9271, MT7610U/7612U/7921AU, RTL8812AU/8814AU and kin) — built-in laptops cards are out of scope. It is an interactive TUI, not a flag-driven batch tool: everything happens after launch, in the app.

## Typical use cases

### Launch the auditor on a supported USB adapter

Start the TUI and get from splash screen to channel-hopping scanner.

```
wifit3
```

Outcome: opens the adapter picker showing detected supported USB adapters; selecting one and pressing `START` performs the one-time setup (on Linux a `pkexec`/sudo prompt writes udev permissions and driver blocklists under `/etc/modprobe.d/`, on Windows a UAC prompt binds WinUSB), detaches the adapter from the OS Wi-Fi stack, and opens the real-time scanner dashboard — from there pick a target to focus for handshake/PMKID capture or WPS attacks. The `Uninstall` button on the splash screen reverses the driver takeover.

*No corpus evidence — derived from upstream documentation (launch flow and features verified against the derv82/wifit3 README).*
