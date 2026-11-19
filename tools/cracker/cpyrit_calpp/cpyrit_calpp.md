---
name: "cpyrit_calpp"
gentoo-category: "app-crypt"
version: "9999"
homepage: "http://code.google.com/p/pyrit/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cpyrit_calpp

> AMD CAL++ GPU backend module for Pyrit's WPA/WPA2-PSK cracker.

## What it's for

Pyrit pre-computes WPA/WPA2-PSK handshake material into space/time-tradeoff databases; cpyrit_calpp is the optional backend module that turns AMD/ATI graphics cards (programmed through the ATI Stream SDK's CAL++ interface) into additional compute cores for that job. Once installed alongside Pyrit, each supported GPU is enumerated as a `CAL++ Device` core and Pyrit distributes pairwise-master-key computation across it automatically. The module has no command-line interface of its own — everything runs through the `pyrit` tool — and it belongs to the classic Python 2 line of Pyrit.

## Typical use cases

### Verify the CAL++ cores are registered

Confirm Pyrit sees the AMD GPUs after installing the module.

```
pyrit list_cores
```

Outcome: lists every detected compute core; each usable AMD card appears as a `CAL++ Device #N '<name>'` entry alongside the CPU cores. No CAL++ line means the module was not built or loaded.

### Benchmark the GPU cores

Measure how fast the GPUs compute PMKs relative to the CPU.

```
pyrit benchmark
```

Outcome: calibrates briefly, then reports a PMKs/s figure for every core, including the CAL++ devices, so GPU and CPU throughput can be compared before batching work.

*No corpus evidence — derived from upstream documentation.*
