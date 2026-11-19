---
name: "phonesploit"
categories:
  - blackarch-mobile
version: "51.0193f9e"
homepage: "https://github.com/metachar/PhoneSploit"
packaged-by: [blackarch]
blackarch-builddate: "2020-01-22"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "metachar/PhoneSploit"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# phonesploit

> Menu-driven Python tool that drives adb against Android devices with an exposed ADB port — an interactive wrapper, not an exploit generator.

## What it's for

PhoneSploit (packaged from metachar's now-deleted repository; an archived copy survives on the Web Archive, and its README itself credits Zucccs/PhoneSploit) is a Python 2 interactive menu for controlling Android devices whose ADB interface is reachable over the network without authentication — the classic open-port-5555 misconfiguration searchable on internet scan engines. Once pointed at such a host, its v1.2 menu offers device-wide operations: port forwarding, netstat, grabbing `wpa_supplicant` (saved Wi-Fi credentials), toggling Wi-Fi on and off, showing MAC and Internet addresses, removing the screen-lock password, extracting an installed app's APK, sending keycodes, and reading battery status and the current foreground activity. Every item is a plain adb command — the point of the tool is demonstrating that an unauthenticated ADB port is full device control. Python 2 only and unmaintained since 2019.

## Typical use cases

### Drive a device over an open ADB port

Launch the interactive menu and pick an action against a reachable device.

```
phonesploit
```

Outcome: the colorized numbered menu opens (the BlackArch package wraps `main_linux.py`); after selecting the target, menu entries such as show current activity, grab wpa_supplicant, extract an app's APK, port forwarding or remove password run the corresponding adb operation and print its output in the terminal. ADB and fastboot must be installed for any of it to work.

*No corpus evidence — derived from upstream documentation.*
