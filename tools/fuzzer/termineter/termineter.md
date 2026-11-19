---
name: "termineter"
categories:
  - blackarch-fuzzer
version: "210.a802f94"
homepage: "https://github.com/rsmusllp/termineter"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "rsmusllp/termineter"
github-stars: 279
github-pushed: "2026-03-17"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# termineter

> Interactive Python framework by Spencer McIntyre (zeroSteiner) for security testing of smart meters over an optical probe.

## What it's for

Termineter speaks the ANSI C1218 and C1219 metering protocols through an ANSI type-2 optical probe attached to a serial port (9600 baud by default), covering the configuration most commonly found on North American meters. It works like a small console framework: modules are selected with `use`, configured with `set` (most importantly the `SERIAL_CONNECTION` device, e.g. /dev/ttyUSB0) and executed with `run`, either interactively or from a resource file. The bundled modules cover identification and information reads, table enumeration/dumping/reading/writing, user-id enumeration, dictionary-based password brute forcing, and operations such as changing the meter id or mode and issuing a remote reset. It cannot reach a meter without physical access to its optical port.

## Typical use cases

### Open an interactive session with the probe attached

```
termineter
```

Outcome: drops you into the termineter console (`use`, `set`, `show`, `connect`, `run`, `info`, ...) where modules are selected and executed one at a time; `-L DEBUG` raises logging verbosity.

### Identify a meter over the optical interface

```
termineter
> set SERIAL_CONNECTION /dev/ttyUSB0
> use get_identification
> run
```

Outcome: opens the serial connection through the optical probe and reads the meter's electronic identification, printing the device's identity fields to the console.

### Replay a scripted session from a resource file

```
termineter -r enum.rc
```

Outcome: executes the console commands listed in `enum.rc` non-interactively, suitable for scripted sweeps across several meters or repeatable evidence collection.

*No corpus evidence — derived from upstream documentation.*
