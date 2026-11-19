---
tool: "blobrunner"
build-system: "windows"
upstream: "https://github.com/OALabs/BlobRunner"
recipe: "upstream"
license: "MIT"
---

# Installing blobrunner

> Windows loader that maps shellcode blobs into memory and jumps to them for debugger analysis.

Source: HackTricks feed admission; upstream [`OALabs/BlobRunner`](https://github.com/OALabs/BlobRunner).

## Prebuilt binaries

Download the compiled x86/x64 executables from the
[releases](https://github.com/OALabs/BlobRunner/releases) page — the fastest
route on an analysis VM.

## Build from source (Windows)

Requires Microsoft Visual C++ Build Tools or Visual Studio.

```bat
:: x86 — from a VS Developer Command Prompt in the repo directory
cl blobrunner.c

:: x64 — from an x64 VS Developer Command Prompt
cl /Feblobrunner64.exe /Foblobrunner64.out blobrunner.c
```

## Not packaged

Windows-only single-file C tool; no Linux/BSD distro packages it. Checked
2026-11-13.

Backlinks: [[reversing]] · [[blobrunner]]
