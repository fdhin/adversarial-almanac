---
name: "pymobiledevice3"
categories:
  - mobile
homepage: "https://github.com/doronz88/pymobiledevice3"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-13"
github-repo: "doronz88/pymobiledevice3"
github-stars: 3033
github-pushed: "2026-11-13"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
github-commits-year: 1634
gh-activity-checked: "2026-11-13"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "GPL3"
---

# pymobiledevice3

> Pure-Python `adb`-equivalent for iOS: lockdownd services, syslog, crash reports, per-process traffic capture, file system and backup access, profile management — no Xcode and no jailbreak required.

## What it's for

`pymobiledevice3` speaks Apple's USB (usbmuxd) and network (RemoteXPC) protocols to a connected iPhone/iPad and exposes them both as a CLI and a Python API. From an offensive/forensics standpoint it is the standard host-side toolkit for iOS: enumerating devices and lockdowld services, pulling crash reports, streaming syslog, dumping contacts without a backup, browsing the media partition over AFC, taking filtered device-wide or per-process packet captures, mounting developer disk images, and installing/removing configuration profiles. Developer services on iOS 17+ work over a tunnel the tool builds for you (Windows/Linux included). It does not bypass passcodes or exploit anything — it operates within what a trusted/paired connection allows, plus developer-service capabilities on unlocked devices.

## Typical use cases

### Enumerate devices and forward a port

```
pymobiledevice3 usbmux list
pymobiledevice3 usbmux forward 8080 8080
```

Outcome: lists UDID, name and connection type of every attached device; the second command forwards host port 8080 to port 8080 on the device over USB — the pattern for reaching an on-device service (e.g. an instrumented app's listener) without a network.

### Stream and filter syslog

```
pymobiledevice3 syslog live -m SpringBoard
```

Outcome: prints the device's live syslog (debug lines included) filtered to lines mentioning SpringBoard; watch for auth events, URL schemes or instrumentation output while exercising a target app.

### Pull crash reports

```
pymobiledevice3 crash pull /path/to/crashes
```

Outcome: copies every crash report from the device into the local directory — the fastest route to a target app's runtime state (stack traces, loaded images) when it dies during testing.

### Capture device traffic to Wireshark

```
pymobiledevice3 pcap --out capture.pcapng
pymobiledevice3 pcap --process backboardd -c 100
```

Outcome: sniffs all device traffic (or only `--process backboardd`, capped at 100 packets) into a pcapng for offline analysis — per-process capture on device, no port mirror needed.

### Browse the media partition

```
pymobiledevice3 afc shell
```

Outcome: opens an interactive shell on the AFC media directory; host tools pipe inside it (`cat photo.heic > photo.heic`), and `--tool jq --tool plutil` exposes them for in-shell plist/JSON triage.

### Dump data without a backup

```
pymobiledevice3 mobilesync dump contacts
```

Outcome: pulls contacts (calendars, bookmarks and mail accounts work the same way) directly via mobilesync — no full backup, no iCloud.

### Take a full or filtered backup

```
pymobiledevice3 backup2 backup --full DIRECTORY
pymobiledevice3 backup2 backup --only messages DIRECTORY
```

Outcome: writes an entire device backup (or only the messages domain) to DIRECTORY; `--only` accepts sms, whatsapp, contacts, call_history, bookmarks and `--only-regex` for filename-level filtering — targeted acquisition that keeps Manifest.db intact.

### Manage configuration profiles

```
pymobiledevice3 profile list
pymobiledevice3 profile install my.mobileconfig
pymobiledevice3 profile remove com.example.profile
```

Outcome: lists, installs and removes `.mobileconfig` profiles on the device — the vehicle for pushing a custom root CA, proxy or MDM-style settings onto a test device.

*No corpus evidence — derived from upstream documentation.*
