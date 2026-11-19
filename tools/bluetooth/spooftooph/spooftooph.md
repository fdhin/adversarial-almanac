---
name: "spooftooph"
categories:
  - blackarch-bluetooth
version: "0.5.2"
homepage: "http://www.hackfromacave.com/projects/spooftooph.html"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2018-11-25"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "Afsalmc/Spooftooph-mirror"
github-stars: 1
github-pushed: "2025-03-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spooftooph

> Clone or randomize a local Bluetooth adapter's Name, Class of Device, and BD_ADDR.

## What it's for

spooftooph rewrites the identity of a local Bluetooth adapter so it presents the Name, Class of Device (CoD), and address of another device — or a fully random one. It is used to test whether Bluetooth gateways, pairing logic, or device-tracking systems actually validate the identity fields they observe, and to make an adapter blend in during authorized assessments. It only changes the local adapter: it does not sniff traffic, crack keys, or attack remote devices beyond scanning for profiles to clone. Root privileges are required, and device selection happens through an ncurses interface.

## Typical use cases

### Set an explicit spoofed identity

Apply a chosen Name, Class, and address to the adapter in one command.

```
spooftooph -i hci0 -n "Headset" -a 00:11:22:33:44:55 -c 0x1c010c
```

Outcome: the adapter on hci0 immediately advertises the given name, address, and class; subsequent inquiries from nearby devices see the spoofed identity instead of the real one.

### Randomize the adapter identity

```
spooftooph -i hci0 -R
```

Outcome: a random NAME, CLASS, and BD_ADDR are generated and applied to hci0 — a quick "look like a different device" reset between test rounds.

### Scan for nearby devices and save profiles to CSV

```
spooftooph -i hci0 -s -w devices.csv
```

Outcome: an ncurses list of discoverable devices in range; selecting an entry clones that device's identity onto the adapter, and the full scan (name, class, address per device) is written to devices.csv for later reuse.

### Replay a previously captured profile list

```
spooftooph -i hci0 -r devices.csv
```

Outcome: profiles from the saved CSV are listed in ncurses for selection, so a device can be cloned again without it being present or re-scanning.

### Continuously impersonate rotating devices

```
spooftooph -i hci0 -t 30
```

Outcome: every 30 seconds the adapter automatically takes on the identity of a random device found in range, cycling until interrupted — useful for testing whether logging systems correlate identity changes.

*No corpus evidence — derived from upstream documentation.*

GitHub: Afsalmc/Spooftooph-mirror is a third-party mirror of the 0.5.2 release in the Kali packaging tree (spooftooph.c verified byte-identical to the SourceForge tarball; only build patches on top).
