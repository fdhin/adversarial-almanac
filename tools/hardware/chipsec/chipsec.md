---
name: "chipsec"
categories:
  - blackarch-hardware
version: "2.0.9.r11.g2545706"
homepage: "https://github.com/chipsec/chipsec"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: python
license: GPL2
meta-checked: "2026-11-09"
github-repo: "chipsec/chipsec"
github-stars: 3322
github-pushed: "2026-11-07"
github-archived: false
github-issues: 31
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 279
gh-activity-checked: "2026-11-09"
---

# chipsec

> Platform Security Assessment Framework — a Python suite for auditing the hardware, firmware (BIOS/UEFI) and platform configuration of x86 systems.

## What it's for

Assessing the security of PC platforms: it exercises the low-level interfaces (SPI flash, MSRs, PCI config space, memory-mapped registers, SMBIOS/ACPI tables, TPM, Intel TXT) and runs a test suite of modules that check platform lock bits, BIOS write protection, boot flow settings and known firmware misconfigurations. Reach for it when validating that a machine's firmware was flashed and locked down correctly, during firmware security research, or to dump/parse flash and platform state from a running OS. It runs on Windows, Linux and the UEFI shell, and nearly everything except passive parsing needs root and direct hardware access. It is a diagnostic and research framework, not an exploitation toolkit.

## Typical use cases

### Run the whole platform check suite

One command audits the machine the framework is running on:

```
sudo chipsec_main -i
```

Outcome: chipsec loads its kernel driver/helper, probes the platform (CPU, PCH, chipset), then runs every applicable module and prints a PASS/FAIL summary per check (BIOS write protection, SMRAM lock, SMM protections, boot media locks, etc.). `-i` skips the platform-support check so modules still run on untested platforms.

### Run a single named module

Target exactly one check instead of the whole suite:

```
sudo chipsec_main -m common.bios_wp
```

Outcome: only the `bios_wp` module executes and prints its verdict and the register values behind it. `-a` passes module-specific arguments (e.g. `-m common.cpu.cpu_info -a eax 0`), and `-lm` / `--list_modules` prints every available module name.

### List and inventory what a platform exposes

The `chipsec_util` entry point is the toolbox half of the framework:

```
sudo chipsec_util --list
```

Outcome: prints every low-level utility command available (spi, mem, io, pci, msr, mmio, smbios, acpi, tpm, uefi, cmos, ec, ...), each of which takes its own subcommands. Any of them run with `--help` shows the subcommand syntax.

### Dump the SPI flash from a running system

Read the firmware image out of the SPI chip through the chipset's internal controller:

```
sudo chipsec_util spi dump firmware.bin
```

Outcome: the SPI flash contents (BIOS region and descriptor) are written to `firmware.bin` in the current directory, ready to be parsed by UEFITool, `binwalk`, or chipsec's own UEFI/UEFI-variable utilities (`chipsec_util uefi`). `chipsec_util spi info` prints chip identity, size and the descriptor regions first.

### Read or write arbitrary hardware registers

Interrogate a specific MSR or memory-mapped register during research:

```
sudo chipsec_util msr read 0x3A
sudo chipsec_util mem dump 0xFED00000 0x100
```

Outcome: the raw value of MSR 0x3A (or a hex dump of the requested physical memory range) is printed — the building blocks for verifying lock bits or chasing firmware behaviour without writing a kernel driver.

*No corpus evidence — derived from upstream documentation.*
