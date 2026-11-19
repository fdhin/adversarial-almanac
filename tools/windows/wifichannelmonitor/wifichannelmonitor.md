---
name: "wifichannelmonitor"
categories:
  - blackarch-windows
version: "1.70"
homepage: "https://www.nirsoft.net/utils/wifi_channel_monitor.html"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-19"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# wifichannelmonitor

> NirSoft utility that captures Wi-Fi traffic on a chosen channel and summarizes access points and clients.

## What it's for

WifiChannelMonitor (NirSoft) is a Windows GUI tool that captures wireless traffic on a single channel you select, using the Microsoft Network Monitor 3.x capture driver (or a plain monitor-mode capture adapter), and turns it into a live summary: every access point seen on that channel with its details, and every client station associated to them, including stations probing for networks. It is useful for wireless surveys and monitoring what actually rides a channel — who is talking to which AP — without a full protocol analyzer. It is a passive monitor, not an attack tool: it does not inject frames, deauthenticate clients or crack keys, and it only sees the one channel it is tuned to. Command-line support is limited to launching with a saved configuration; everything else happens in the GUI.

## Typical use cases

### Monitor one channel's access points and clients

Tune to the channel of interest and watch the station inventory build.

```
WifiChannelMonitor.exe
```

Outcome: the GUI opens; pick the capture driver/adapter and the channel, start capture, and the tool accumulates a table of access points and connected client devices observed on that channel (SSIDs, BSSIDs, signal, and per-AP client lists) — usable as a quick site survey or to find unauthorized APs/clients on your own network.

### Relaunch with a saved configuration

Re-run a previously configured monitoring setup without clicking through settings.

```
WifiChannelMonitor.exe /cfg "c:\config\wcm.cfg"
```

Outcome: starts with the driver, channel and options loaded from the given `.cfg` file instead of defaults — NirSoft documents `/cfg` for repeatable monitoring sessions.

*No corpus evidence — derived from upstream documentation (NirSoft WifiChannelMonitor page).*
