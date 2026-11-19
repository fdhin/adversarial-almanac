---
name: "sgx-step"
categories:
  - hardware
homepage: "https://github.com/jovanbulck/SGX-Step"
github-repo: "jovanbulck/SGX-Step"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: GPL3
github-stars: 479
github-commits-year: 14
gh-checked: "2026-11-14"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# sgx-step

> Research framework for SGX enclave attack development — user-space control of page-table entries and APIC timer interrupts to single-step victim enclaves instruction by instruction.

## What it's for

SGX-Step is an open-source attack framework for side-channel and transient-execution research on Intel x86 in general and SGX platforms in particular: an adversarial Linux kernel driver plus a small user-space OS library (libsgxstep) that let an untrusted host process remap page-table entries, reconfigure the local APIC timer, and register interrupt handlers entirely from user space. For exploit development it is the instrumentation layer that turns a victim enclave into an observable: interrupt the enclave after every single instruction (single-stepping), revoke access rights on chosen code/data pages to bound the attack window, and plug attack-specific spy code into the AEX/interrupt path. It enabled published attack lines — Foreshadow (transient execution), Plundervolt (privileged interrupt/call gates), Nemesis (interrupt-latency fingerprinting) — and its demo app brute-forces a non-constant-time `memcmp` password check character-per-character in linear time, where classic timing attacks are too noisy. This is a build-it-yourself research stack (kernel module, patched SGX SDK, per-CPU timer calibration on an SGX-capable Intel host), not a packaged scanner; expect lab setup work. GPLv3, KU Leuven (Jo Van Bulck).

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Verify the host kernel is configured for single-stepping

```
sudo ./check_sys.sh
```

Outcome: Prints per-check OK/FAIL lines for the recommended kernel parameters, CPU features, and page-table isolation — run after adding the SGX-Step boot parameters to GRUB; all-OK means the host is ready to load the driver.

### Build and load the /dev/sgx-step driver

```
cd kernel/
./install_SGX_driver.sh
make clean load
```

Outcome: Installs the underlying SGX driver (in-tree /dev/sgx_enclave when available, otherwise the out-of-tree /dev/isgx), then builds and loads the SGX-Step module exposing /dev/sgx-step for libsgxstep.

### Install the AEP-patched Intel SGX SDK

```
cd sdk/intel-sdk/
./install_SGX_SDK.sh
source /opt/intel/sgxsdk/environment
```

Outcome: Checks out linux-sgx v2.29 with SGX-Step's patches (custom Asynchronous Exit Pointer registration for catching enclave exits) and installs it under /opt/intel/sgxsdk; source the environment file in every shell or add it to ~/.bashrc before building apps.

### Stress-test user-space interrupt handling

```
cd app/selftest/idt
make run
```

Outcome: Fires interrupts into a user-space IDT handler in an infinite loop — a stability check that the timer/IDT takeover works on this host before aiming it at a victim enclave; stop with Ctrl-C.

### Single-step a benchmark enclave and calibrate the timer interval

```
cd app/bench
NUM=100 make parse
```

Outcome: Builds libsgxstep, a nop-slide victim enclave and the attacker host process, runs the attack non-interactively, and post-processes the instruction-pointer trace into counts of single-steps, zero-steps and multi-steps; tune SGX_STEP_TIMER_INTERVAL in libsgxstep/config.h until single-steps dominate — too many zero-steps means raise the interval, multi-steps means lower it (the per-CPU reference table in the README lists known-good starting values).

### Confirm the APIC timer survived a run

```
watch -n0.1 "cat /proc/interrupts | grep 'Local timer interrupts'"
```

Outcome: Live per-core count of local timer interrupts — after a killed or crashed attack process this confirms libsgxstep restored APIC/IDT state; a frozen counter means the kernel is one step from a panic.
