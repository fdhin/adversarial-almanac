---
name: "sdrpp"
categories:
  - blackarch-radio
version: "nightly.r363.gf10fb98"
homepage: "https://www.sdrpp.org"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: cmake
license: GPL3
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sdrpp

> SDR++ — cross-platform, bloat-free SDR receiver with a modular source/sink plugin architecture.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SDR++ (binary `sdrpp`) is an open-source SDR receiver aiming to stay simple and unbloated: multi-VFO operation on one waterfall, SIMD-accelerated DSP, and wide hardware support split between dedicated source modules (RTL-SDR, HackRF, Airspy, PlutoSDR, SDRplay and others) and a generic SoapySDR module. Modules are loaded per the `modules` list in `config.json`, and decoders/recorders can be added the same way, so the same binary serves as a casual receiver or a dedicated decoder box. Windows, Linux, macOS and BSD are supported. It is receive-focused; the frequency visual is a classic waterfall, not a signal analyzer with protocol decoding.

## Typical use cases

### Launch the receiver

```
sdrpp
```

Outcome: the main window opens with the default source; pick a source module in the menu, tune with the waterfall/VFO, and `config.json` is created on first run in the resources directory with the module list and UI settings.

### Run against an explicit resources directory

```
sdrpp -r root_dev
```

Outcome: SDR++ uses `root_dev` as its resources root — `config.json` and module data are read from and written there instead of the default location, which is how self-contained or multiple parallel configurations (for example a separate decoding setup) are kept apart.
