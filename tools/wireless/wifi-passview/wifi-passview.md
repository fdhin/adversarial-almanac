---
name: "wifi-passview"
categories:
  - wireless
version: "git (2026-08-31)"
homepage: "https://github.com/warengonzaga/wifi-passview"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "warengonzaga/wifi-passview"
github-stars: 340
github-pushed: "2026-08-31"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 8
---

# wifi-passview

> Windows batch script that recovers Wi-Fi passwords saved on the machine through an interactive menu.

## What it's for

WiFi Passview, by Waren Gonzaga, is a standalone Windows batch (`.bat`) program that recovers wireless passwords stored by Windows — it wraps `netsh wlan` profile queries so you never read `Key Content` output by hand, the same idea as NirSoft's passview tools but for Wi-Fi. From its numbered menu you can dump every saved profile's password, extract the password for one specific SSID, save the results to a file, generate a WLAN report (the one operation that needs an administrator prompt), and optionally upload collected passwords to the cloud via the file.io API. It needs no admin rights for extraction, is Windows-only, and — per its author — is intended for recovering your own credentials, not for stealing others'. It is menu-driven, not a scripting tool: there are no command-line flags.

## Typical use cases

### Recover all stored Wi-Fi passwords

Run the released batch file on the Windows machine whose saved credentials you want back.

```
wifi-passview-vX.X.X.bat
```

Outcome: opens the interactive `WiFiPassview` menu; the default option enumerates every saved WLAN profile and prints its SSID and key content in seconds without admin rights, and further menu entries extract a single SSID's password, save the output to a file, generate a WLAN report, or upload results to file.io.

*No corpus evidence — derived from upstream documentation (repo README and released batch script).*
