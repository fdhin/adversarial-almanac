---
name: "ipsw"
categories:
  - firmware
homepage: "https://github.com/blacktop/ipsw"
origin: "hacktricks"
recipe: "upstream"
packaged-by:
  - aur
  - nixpkgs
build-system: go
meta-checked: "2026-11-13"
github-repo: "blacktop/ipsw"
github-stars: 3890
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "MIT"
---

# ipsw

> Go toolkit for Apple firmware research: download/unpack IPSWs and OTAs, inspect Mach-O binaries and dyld shared caches, extract and diff kernelcaches, and drive connected iOS devices.

## What it's for

`ipsw` is a single-binary CLI for everything Apple-firmware: fetching IPSW/OTA files by device identifier, extracting their components (kernelcaches, dyld shared caches, firmware volumes), parsing IMG4 containers, and then analyzing what it extracted — Mach-O load commands, symbols, code signatures and entitlements, Objective-C header dumps from the shared cache, KEXT listings and syscall tables, plus diffing two firmware builds. It also has a device side (`idev`) that lists devices and apps, transfers files over AFC, captures syslog and mounts developer images — overlapping with pymobiledevice3 but focused on firmware research. Analysis is local/offline once the firmware is downloaded; an optional `ipswd` daemon exposes a REST API. It is a research tool, not an exploit or bypass.

## Typical use cases

### Download firmware for a device

```
ipsw download ipsw --device iPhone16,1 --latest
```

Outcome: fetches the latest IPSW for iPhone16,1 (iPhone 15 Pro) from Apple's servers into the current directory — the starting point for any offline target analysis.

### Inspect and extract from an IPSW

```
ipsw info firmware.ipsw
ipsw extract --kernel firmware.ipsw
```

Outcome: `info` identifies build, device and component layout; `extract --kernel` pulls the kernelcache out of the archive for further analysis.

### Diff two firmware builds

```
ipsw diff old.ipsw new.ipsw
```

Outcome: reports what changed between two builds — new/removed binaries, changed kernel extensions and version deltas — the quick triage of a security release.

### Explore a dyld shared cache

```
ipsw dyld info dyld_shared_cache_arm64e
ipsw dyld extract dyld_shared_cache_arm64e Foundation
```

Outcome: summarizes cache structure and images, then extracts the Foundation dylib for use in other tools (extracted dylibs still reference cache metadata, so prefer in-cache analysis when possible).

### Dump Objective-C headers from the cache

```
ipsw class-dump dyld_shared_cache_arm64e SpringBoardServices --headers -o headers
```

Outcome: writes decompiled Objective-C header files for the named framework into `headers/` — class/method surface for private frameworks without running anything.

### Inspect and disassemble a Mach-O

```
ipsw macho info binary --arch arm64e
ipsw macho disass binary --arch arm64e --symbol _main
ipsw macho search binaries_dir/ --import 'CCCrypt'
```

Outcome: prints load commands/entitlements for a chosen slice of a universal binary, disassembles from `_main`, and sweeps a directory for every binary importing `CCCrypt` — e.g. finding all crypto call sites in a dump of system binaries.

### List and extract kernel extensions

```
ipsw kernel kexts kernelcache
ipsw kernel extract kernelcache com.apple.driver.ASIOKit -o kexts
```

Outcome: enumerates all KEXTs in a kernelcache and pulls a specific one out by bundle ID for inspection — attack-surface mapping of driver code.

### Work with a connected device

```
ipsw idev list
ipsw idev syslog
```

Outcome: lists attached devices and streams their syslog — correlate live behavior with the firmware artifacts analyzed above.

*No corpus evidence — derived from upstream documentation.*
