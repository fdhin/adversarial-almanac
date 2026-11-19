---
name: "sdrsharp"
categories:
  - blackarch-radio
version: "1.0.0.1457"
homepage: "https://airspy.com"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-11-11"
build-system: other
license: LicenseRef-SDRSharp
meta-checked: "2026-11-09"
github-repo: "cgommel/sdrsharp"
github-stars: 271
github-pushed: "2020-04-11"
github-archived: false
github-issues: 9
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sdrsharp

> SDRSharp — the classic Windows .NET SDR receiver, packaged to run under Mono on Linux.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SDRSharp (SDR#) is the long-popular .NET SDR receiver from Airspy, originally written for Windows. The Linux package repackages the upstream Windows release instead of porting it: the .exe assemblies are installed under `/opt/sdrsharp` and AOT-compiled for Mono, with the system `librtlsdr` and PortAudio libraries linked in under `.dll` names, and `/usr/bin` wrapper scripts (`sdrsharp`, plus the bundled Airspy utilities) launch them through Mono. It is a GUI receiver with plugin-based front ends (RTL-SDR, Airspy, FUNcube, HackRF, RTL-TCP and more in the source tree) — expect Mono quirks rather than native integration, and note the license is MS-RSL-like rather than a normal OSS one.

## Typical use cases

### Launch the receiver under Mono

```
sdrsharp
```

Outcome: the wrapper runs SDRSharp.exe through Mono; the familiar .NET GUI opens, and selecting an RTL-SDR source tunes via the system rtl-sdr library — the standard way to run SDR# hardware-free of Windows.

### Open a standalone spectrum tool

```
spectrumspy
```

Outcome: launches the bundled SpectrumSpy utility from the same package — a simple wideband spectrum/sweep view over the supported hardware, useful for antenna and filter checks without configuring the full receiver.
