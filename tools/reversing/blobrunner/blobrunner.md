---
name: "blobrunner"
categories:
  - reversing
homepage: "https://github.com/OALabs/BlobRunner"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: windows
meta-checked: "2026-11-13"
github-repo: "OALabs/BlobRunner"
github-stars: 642
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "MIT"
---

# blobrunner

> Minimal Windows loader that maps a shellcode blob into allocated memory and jumps to it, so a debugger can single-step raw shellcode extracted during malware analysis.

## What it's for

Malware routinely drops position-independent shellcode blobs that no disassembler wants to load as a PE. BlobRunner closes that gap with the smallest possible shim: it allocates memory, copies the file into it, and transfers execution to the blob's base (or a chosen offset) — giving your debugger (x64dbg, WinDbg, IDA) a live process to step through instead of static bytes. Reach for it whenever analysis has produced a raw `.bin` shellcode stage and you need to watch it run: unpacking stubs, injected loaders, embedded payload stages. It does no analysis itself and never inspects the blob — it is purely a launch pad, pausing before the jump so you can set breakpoints. It is Windows-only (x86 and x64 builds); on Linux the equivalent pattern is a small mmap/execve harness or `strace`-driven loaders.

## Typical use cases

### Debug into a shellcode blob

```
BlobRunner.exe shellcode.bin
```

Outcome: run under your debugger (open `BlobRunner.exe` in x64dbg with the blob as argument); BlobRunner allocates the region, copies the file in and pauses before jumping — set a breakpoint on the jump, then step into the shellcode with the blob mapped at the printed base address.

### Start execution at an offset inside the blob

```
BlobRunner.exe shellcode.bin --offset 0x0100
```

Outcome: execution transfers to base + `0x0100` instead of the blob's first byte — used when the interesting stage begins partway into the file (e.g. a header or XOR-key region precedes the code).

### Let the JIT debugger attach on first execution

```
BlobRunner.exe shellcode.bin --jit
```

Outcome: the allocated page is mapped non-executable first, so the attempt to run the shellcode raises an `0xC0000005` access violation that hands the process to the configured Just-In-Time debugger; once attached, restore execution with x64dbg's `setpagerights <region>, ExecuteReadWrite` using the address BlobRunner printed.

### Debug 64-bit shellcode

```
blobrunner64.exe shellcode.bin
```

Outcome: the x64 build (inline assembly being unsupported by the x64 MSVC compiler) creates a **suspended thread** at the shellcode entry instead of jumping — place a breakpoint at the thread entry point, then resume the thread to start stepping the 64-bit blob.

### Load a test blob you generated yourself

```
msfvenom -a x86 --platform windows -p windows/exec cmd=calc.exe -o test.bin
BlobRunner.exe test.bin
```

Outcome: a known-good position-independent payload is produced and then run under the debugger — a quick way to validate a reversing workflow or demo shellcode behaviour without a live sample.

*No corpus evidence — derived from upstream documentation.*
