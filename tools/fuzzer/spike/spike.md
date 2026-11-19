---
name: "spike"
categories:
  - fuzzing
  - vulnerability
version: "1.1.0-6"
homepage: "https://github.com/riscv/riscv-isa-sim"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-07-03"
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "riscv/riscv-isa-sim"
github-stars: 3275
github-pushed: "2026-11-07"
github-archived: false
github-issues: 420
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 577
gh-activity-checked: "2026-11-09"
---

# spike

> Spike — the reference RISC-V ISA simulator (riscv-isa-sim), used to run and debug RISC-V binaries on a host.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Spike (riscv-isa-sim) is the golden-model functional simulator for the RISC-V architecture: it implements the base ISAs (RV32I/RV64I, RV32E/RV64E) and the standard extensions (M/A/F/D/Q/C, vector, bit-manipulation, scalar cryptography, hypervisor, and the supervisor memory-management extensions) for one or more harts, executing RISC-V ELF binaries — typically atop the riscv-pk proxy kernel, which supplies host syscalls. It is the standard vehicle for running architecture tests, booting simple kernels and co-simulating RTL against a reference model; its interactive debug monitor exposes registers and memory while single-stepping. Note the name collision: this is an instruction-set simulator with no relation to security fuzzing — Immunity's SPIKE network protocol fuzzer is a different tool covered at [[spike-fuzzer]], and Kali packages that fuzzer under the bare name `spike`.

## Typical use cases

### Run a RISC-V binary under the proxy kernel

Executes a cross-compiled ELF on the simulated hart.

```
spike pk hello
```

Outcome: loads the riscv-pk proxy kernel together with the `hello` ELF, simulates the hart, and runs the program to completion with its output on stdout — the basic smoke test for a cross-compiled binary (`riscv64-unknown-elf-gcc -o hello hello.c`).

### Debug a program instruction by instruction

Drops the simulation into the interactive debug monitor.

```
spike -d pk hello
```

Outcome: stops at the first instruction and gives the `:` prompt, where commands like `reg 0 a0`, `fregs 0 ft0` and `mem 2020` inspect integer and floating-point registers and physical memory as you step the simulation.
