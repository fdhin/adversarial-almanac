---
name: "fridump"
categories:
  - blackarch-forensic
version: "23.3e64ee0"
homepage: "https://github.com/Nightbringer21/fridump"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Nightbringer21/fridump"
github-stars: 861
github-pushed: "2024-08-07"
github-archived: false
github-issues: 27
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fridump

> Dump the accessible memory of a running process through the Frida instrumentation framework, locally or from a USB-connected mobile device.

## What it's for

fridump (by Nightbringer21) is a universal memory dumper built on Frida. From a Windows, Linux or macOS host it enumerates the memory ranges a process has mapped and writes each range to its own file in an output directory (default `dump/`), which you then mine with strings, a debugger or signature scanning. It works against local processes and against apps on USB-attached iOS or Android devices, where a running frida-server (root on Android) is a prerequisite. It does not analyze the dumps — dumping is all it does. BlackArch installs it as the `fridump` command.

## Typical use cases

### List candidate processes before dumping

Identify the process name or bundle identifier to target.

```
frida-ps -U
```

Outcome: prints the process list of the USB-connected device (drop `-U` to list local processes); use the reported name as fridump's target argument.

### Dump an app on a USB-connected device

Acquire the memory of a mobile application.

```
fridump -U Safari
```

Outcome: one file per accessible memory range written to `./dump/`; transfer the directory off the device side for analysis.

### Dump an Android app and run strings automatically

Collect memory plus pre-extracted strings in one pass.

```
fridump -U -s com.example.app -o /tmp/dumps
```

Outcome: dump files land in `/tmp/dumps/` and each is additionally processed with strings, with the string output saved alongside so secrets like tokens or keys can be grepped without running strings again.

### Include read-only regions of a local process

Broaden a local dump beyond writable memory.

```
fridump -r -o /tmp/dumps target
```

Outcome: read-only mapped regions (code, rodata) are dumped too, producing more data and more read errors; raise `--max-size` (default 20971520 bytes) if ranges are skipped for exceeding the per-file cap.

*No corpus evidence — derived from upstream documentation.*
