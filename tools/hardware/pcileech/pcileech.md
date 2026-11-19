---
name: "pcileech"
categories:
  - blackarch-hardware
version: "4.19.5"
homepage: "https://github.com/ufrisk/pcileech/releases"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-01"
build-system: scripts
license: AGPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "ufrisk/pcileech"
github-stars: 7982
github-pushed: "2026-10-05"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
---

# pcileech

> Reads and writes target-system memory over PCIe DMA using FPGA or USB3380 capture hardware — no driver or software footprint on the target.

## What it's for

Direct memory access to a live machine from outside: a supported FPGA board (Squirrel/CaptainDMA-class, AC701+FT601, ZDMA, over Thunderbolt/OCuLink/USB-C) or a PLX USB3380 card sits on the target's PCIe bus and DMAs its physical memory while the target OS runs unaware. On top of that channel PCILeech dumps memory, searches it, patches running processes, mounts live RAM and the target file system, and loads kernel implants ("KMDs") that unlock full-memory access on USB3380 hardware and enable file pull/push, shell spawning and logon-password removal on Windows. It also works without hardware against dump files and live acquisition sources (DumpIt, WinPmem, VMware, remote LeechAgent) via its LeechCore back end. Targets are x64 UEFI/Linux/FreeBSD/Windows. Defeating it is exactly what IOMMU/VT-d and modern OS DMA-protection do — targets with those enabled (default on recent Windows and Linux) resist or break these attacks. This is invasive equipment for authorized testing and research.

## Typical use cases

### Dump all target memory

With FPGA hardware attached and a kernel module already resident at a known address:

```
pcileech dump -kmd 0x7fffe000
```

Outcome: the tool locks onto the DMA device and streams physical memory to a dump file, printing per-region progress. `-memmap auto` instead derives the memory map from the target's registry so unmapped holes are skipped (safer for the target); `-min 0x0 -max 0x21e5fffff -force` bounds the range and keeps going past unreadable pages. The dump is analyzable in any memory-forensics tool, or feed it to `memprocfs` for a live-mounted view.

### Search memory for a signature

```
pcileech search -sig unlock_win10x64 -all
```

Outcome: physical memory is scanned page by page for the named signature pattern from the signature files shipped with the release; matching addresses are printed. `-all` reports every match instead of stopping at the first — the usual precursor to a targeted `patch` or `write`, and `-pid 344` scopes the search to one process's virtual address space.

### Write bytes into target memory

```
pcileech write -min 0x1003 -in 11223344
```

Outcome: the given bytes are DMA-written at physical address 0x1003 (input can also be a file: `-min 0x140000000 -in replace.bin`). Add `-force` for protected/MMIO ranges — with the documented risk of freezing the target. The per-address in-memory view counterpart is `pcileech pagedisplay -min 0x1000`, which renders one page of target memory.

### Patch a process via a signature

```
pcileech patch -pid 432 -sig unlock_win10x64.sig
```

Outcome: the process's memory is searched for the signature and patched per the sig file's instructions — the packaged signatures implement things like removing the logon-password check, so a subsequent login succeeds without knowing the password. `pcileech lx64_filepull -help` shows the equivalent per-implant options for pulling files off a Linux target.

### Mount the target's RAM and file system, or inspect the PCIe link

```
pcileech mount -kmd 0x11abc000
pcileech tlp -vv -wait 1000
pcileech probe
```

Outcome: `mount` exposes live target memory (and, with the KMD, the file system) as a local mounted drive for browsing with normal tools; `tlp` captures and prints raw PCIe transaction-layer packets from the FPGA for link-level analysis; `probe` (FPGA only) enumerates which memory ranges the target actually answers reads on — the reconnaissance step before committing to a full dump.

*No corpus evidence — derived from upstream documentation.*
