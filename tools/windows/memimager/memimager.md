---
name: "memimager"
categories:
  - blackarch-windows
version: "1.0"
homepage: "http://ntsecurity.nu/toolbox/memimager/"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# memimager

> Historical Windows memory-acquisition tool that dumps physical RAM through the NtSystemDebugControl syscall, no driver required.

## What it's for

MemImager is a small Windows console utility from the ntsecurity.nu toolbox that acquires a physical memory dump by calling the undocumented `NtSystemDebugControl` syscall (control code 10) instead of loading a kernel driver: it requests physical pages page by page and writes them to a dump file, which can then be processed with standard forensic tools.

It is a historical artifact of early-2000s acquisition technique. Per the author's write-up, the syscall path works on Windows XP and Windows Server 2003 SP0, fails with `STATUS_INVALID_INFO_CLASS` on Windows 2000 and with `STATUS_NOT_IMPLEMENTED` on Server 2003 SP1, and modern Windows versions block this kind of debugger access entirely (current tools use a signed kernel driver instead). Use it to understand or reproduce legacy acquisition on period-correct lab systems, not on contemporary targets. No command-line usage is documented on the author's page, so this note is description-only.

*No corpus evidence — derived from upstream documentation.*
