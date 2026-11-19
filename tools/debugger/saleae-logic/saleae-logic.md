---
name: "saleae-logic"
categories:
  - blackarch-debugger
version: "2.3.47"
homepage: "https://www.saleae.com/downloads"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2022-03-19"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# saleae-logic

> The Saleae Logic 2 application — a GUI for Saleae USB logic analyzers that captures digital and analog signals, decodes protocols, and exports the data.

*No corpus evidence — derived from upstream documentation. This is proprietary vendor software distributed from [saleae.com/downloads](https://www.saleae.com/downloads); it has no canonical GitHub repository.*

## What it's for

This is the desktop software that drives Saleae's Logic 8 / Pro 8 / Pro 16 (and original Logic) USB logic analyzers. You configure channels, sample rates, and trigger conditions; capture a window of digital and/or analog signal; then use the built-in protocol analyzers (I2C, SPI, UART/serial, CAN, USB, and more) to decode traffic into readable packets, take timing measurements with the GUI markers, and export samples or decoded data as CSV. It is the tool to reach for when the question is "what is actually on this bus" — firmware talking to a sensor, a bootloader's UART output, an SPI flash read. It is not an oscilloscope replacement (sample rates and voltage ranges are modest), it is closed-source freeware rather than open source, and it needs a physical Saleae device attached for real captures (a built-in demo device lets you explore the UI without hardware).

## Typical use cases

### Launch the analyzer GUI

```
saleae-logic
```

Outcome: opens the Logic 2 window. The device sidebar lists the connected analyzer (or the demo device); from there you set channels, sample rate, trigger, and hit Start to capture. The packaged launcher name comes from the BlackArch package; the vendor's AppImage runs the same application directly.

### Script captures through the automation API

```python
from saleae import automation

mgr = automation.Manager.launch()   # starts a Logic 2 process with the automation server
print(mgr.get_app_info())           # handshake: application and API version of the server
```

Outcome: a headless Python process (package `logic2-automation`, works with recent Logic 2 releases) driving the software — starting captures, configuring devices, and exporting data without clicking the GUI. `get_app_info()` reports the API version so you can bail out early on a client/server mismatch. Alternatively enable the automation server in the GUI's preferences and connect to the already-running application on its default port, 10430.
