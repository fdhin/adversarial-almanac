---
name: "openocd"
categories:
  - hardware
version: "1:0.12.0-6"
homepage: "https://openocd.org"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-08-27"
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "openocd-org/openocd"
github-stars: 2376
github-pushed: "2026-11-04"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 533
gh-activity-checked: "2026-11-09"
---

# openocd

> Open on-chip debugger — drives JTAG/SWD probes to debug, flash and test embedded targets over GDB, telnet and Tcl.

## What it's for

The bridge between a debug probe (ST-Link, J-Link, FTDI-based adapters, CMSIS-DAP, and many board-integrated ones) and a microcontroller or SoC. OpenOCD talks JTAG or SWD to the target's debug tap, then exposes that connection three ways: a GDB remote server (load ELF, set breakpoints, inspect memory), a telnet command port, and a Tcl command channel — which makes it scriptable for both interactive debugging and automated flash programming. It knows thousands of chips' flash controllers (internal flash, CFI/NAND/NOR, QSPI), so erase/write/verify and FPGA bitstream loading work out of the box. Configuration is compositional: one or more `-f` config files combine a probe `interface/` config, a `target/` config for the chip (or a `board/` config that bundles both), plus `-c` snippets for arbitrary Tcl. It does not include a debugger UI — pair it with gdb (or IDA, which also speaks the GDB protocol).

## Typical use cases

### Attach to a board with its bundled config

```
openocd -f board/stm32f4discovery.cfg
```

Outcome: OpenOCD opens the probe, resets the target tap, and starts three servers: GDB on TCP 3333, telnet on 4444, and an optional TCL port. The console prints the target's detected core and flash banks. Connect gdb with `target extended-remote localhost:3333` and debug as if the chip were a native process.

### Compose interface and target configs explicitly

```
openocd -f interface/stlink.cfg -c "transport select swd" -f target/stm32l0.cfg
```

Outcome: the ST-Link adapter is selected, switched to SWD, and matched to an STM32L0 target definition — the pattern for boards that have no bundled `board/` file, or when overriding a probe or transport. The same shape works for JTAG (e.g. `-f interface/ftdi/jtagkey2.cfg -c "transport select jtag" -f target/ti/calypso.cfg`).

### Flash firmware in one shot

```
openocd -f interface/stlink.cfg -f target/stm32l0.cfg -c "program firmware.elf verify reset exit"
```

Outcome: the `program` Tcl command halts the target, erases the affected flash, writes `firmware.elf`, verifies it, resets the chip to run the new image, and `exit` tears the servers down — a single command suitable for CI or scripted deployment, no gdb needed.

### Drive targets from a script or telnet

```
nc localhost 4444
```

Outcome: the telnet command port accepts OpenOCD commands interactively — `reset halt` to hold the core at reset, `flash banks`/`flash info 0` to inspect flash, `mdw`/`mww` for memory reads and writes, `reg` for registers. The same commands via `-c` on the command line turn any of these steps into scripts.

*No corpus evidence — derived from upstream documentation.*
