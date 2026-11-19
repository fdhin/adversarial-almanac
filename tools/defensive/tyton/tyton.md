---
name: "tyton"
categories:
  - blackarch-defensive
version: "80.56494f3"
homepage: "https://github.com/nbulischeck/tyton"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-11"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nbulischeck/tyton"
github-stars: 371
github-pushed: "2021-11-13"
github-archived: true
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tyton

> Kernel-mode Linux rootkit hunter that audits the live kernel for hooks and hidden modules.

## What it's for

tyton is a loadable kernel module (Linux 4.4.0-31 and newer) that performs rootkit detection from inside the kernel itself, where the adversary operates: it hunts hidden modules, syscall-table hooks, network-protocol-handler hooks, netfilter hooks, zeroed process inodes, file-operations hooks, and interrupt-descriptor-table (IDT) hooks. Findings are written to the kernel log, and a bundled userland daemon can surface them as desktop notifications. The upstream repository is archived and development stopped in 2021, so treat it as a focused kernel-integrity auditor for older lab systems — it may need work to build against current kernels. The BlackArch package ships it as a DKMS module that rebuilds on kernel upgrades.

## Typical use cases

### Run one kernel-integrity scan

```
sudo insmod tyton.ko
```

Outcome: the module loads, runs its checks immediately, and reports any detected hook or hidden module to the kernel ring buffer — read the results with `dmesg | grep -i tyton` or `journalctl -k`.

### Keep auditing continuously on an interval

```
sudo insmod tyton.ko timeout=5
```

Outcome: instead of scanning once, the module re-executes its full scan every 5 minutes (`timeout=X` sets the interval in minutes), logging anomalies as they appear — suited to long monitoring sessions on a suspect host.

### Load the packaged DKMS module

```
sudo modprobe tyton timeout=5
```

Outcome: loads the module that DKMS built for the running kernel (same parameter semantics as `insmod`), so the hunter survives kernel upgrades without a manual rebuild.

*No corpus evidence — derived from upstream documentation.*
