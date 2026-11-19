---
name: "netstumbler"
categories:
  - blackarch-windows
version: "0.4.0"
homepage: "http://www.netstumbler.com/downloads/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/netstumbler"
github-stars: 0
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netstumbler

> Classic Windows wardriving tool that discovers and logs nearby 802.11 access points.

## What it's for

NetStumbler (final version 0.4.0) is the early-2000s wardriving standard for Windows: it detects wireless access points and lists SSID, MAC address, channel, signal-to-noise ratio and WEP status, optionally tagging each reading with GPS coordinates for mapping drives. It is closed-source freeware with a GUI only — there is no command-line interface, it depends on the NDIS 5.1-era Wi-Fi stack (Windows 2000/XP) and a supported adapter, and it predates WPA2-era tooling; modern replacements include Kismet on Linux. The BlackArch package ships the original installer as a Windows artifact, and the `github-repo` above is a third-party mirror of that same installer, not an upstream source tree.

## Typical use cases

### Inspect the packaged installer artifact

Confirm what the package ships before moving it into an analysis VM.

```
file /usr/share/windows/netstumbler/netstumblerinstaller_0_4_0.exe
```

Outcome: reports a PE32 Windows executable wrapped in a Nullsoft (NSIS) self-extracting installer; run it inside a Windows 2000/XP-era analysis VM with a supported wireless NIC to use the GUI scanner.

### Verify the artifact's checksum before lab use

Pin the exact binary you will run in the VM.

```
sha256sum /usr/share/windows/netstumbler/netstumblerinstaller_0_4_0.exe
```

Outcome: prints the installer's SHA-256 digest for provenance records; compare it against the mirror's copy to detect substitution.

*No corpus evidence — derived from the upstream site and inspection of the packaged installer artifact.*
