---
name: "dutas"
categories:
  - blackarch-binary
version: "10.37fa3ab"
homepage: "https://github.com/dungtv543/Dutas"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "dungtv543/Dutas"
github-stars: 50
github-pushed: "2016-07-28"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dutas

> Emulates Windows x86 PE files and shellcode in a faked process environment and reports the Windows APIs they call.

## What it's for

Dutas (its engine is `UniAna.py`) is an emulation harness for suspicious
32-bit Windows code: instead of running a sample on a real system, it
executes the x86-32 code inside the Unicorn emulator against a hand-built
Windows environment — PEB, TEB, loader structures and stubs for kernel32,
urlmon, advapi32 and user32 — and reports which API functions the sample
reaches. That gives a fast behavioral read on downloaders and droppers
(URLs fetched, files written, registry keys touched) without a live OS. It
is 2016 research code: Windows x86 32-bit input only, and API coverage is
limited to the stubbed functions. The BlackArch package wraps it as
`/usr/bin/dutas` (Python 2).

## Typical use cases

### Analyze a 32-bit PE sample offline

```
dutas -p sample.exe
```

Outcome: the PE is mapped into the emulator, imports resolve against the
stubbed DLLs, and execution runs while each emulated API call is printed
with its arguments (e.g. `Call URLDownloadToFileA (Url=..., LocalPath=...)`,
`Call CreateFileA (filename=...)`) — an offline API-call trace of the
sample.

### Emulate raw shellcode

```
dutas -s shellcode.bin
```

Outcome: the raw x86-32 blob is executed in the same fake environment with
no PE parsing involved, and the report shows the API sequence the shellcode
attempts (downloaders, droppers and keylogging-style calls stand out
immediately).

*No corpus evidence — derived from upstream documentation.*
