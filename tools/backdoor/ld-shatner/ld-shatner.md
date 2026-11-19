---
name: "ld-shatner"
categories:
  - blackarch-backdoor
version: "4.5c215c4"
homepage: "https://github.com/sduverger/ld-shatner"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-30"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sduverger/ld-shatner"
github-stars: 50
github-pushed: "2011-10-28"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ld-shatner

> Injects code between the ELF dynamic loader and a target binary by patching ld-linux itself — an intrusive alternative to LD_PRELOAD that runs a payload before the program's entry point.

## What it's for

Running a payload inside any dynamically linked ELF executable without modifying that executable's own code or relying on environment variables. ld-shatner embeds a loader routine and the payload into a copy of `ld-linux`, producing a hooking dynamic loader; binaries repointed at that loader (with the bundled `interpatch`) execute the embedded code before their own entry point and then start normally. Reach for it when studying or demonstrating loader-stage code injection: the loader is the one component nearly every dynamically linked binary shares, so one patched loader can hook a whole system's worth of executables. It is 2011-era 32-bit x86 research code and is not maintained; it does not attempt to evade modern integrity checks on the loader.

## Typical use cases

### Build a hooked dynamic loader from a payload

Combine a copy of the system loader with a position-independent payload ELF:

```
cp /lib/ld-linux.so.2 .
./ld-shatner ld-linux.so.2 obj.elf
```

Outcome: writes `ld-hook.so`, a modified loader whose entry first runs the code from `obj.elf` (the bundled sample payload prints `ld-hook`) and then continues with normal program loading. The repository's `obj.c` shows how the payload ELF is constructed.

### Repoint a binary at the hooked loader

```
sudo cp ld-hook.so /lib/
./interpatch ./demo-app
```

Outcome: `interpatch` rewrites the binary's interpreter (`PT_INTERP`) to reference the hooked loader, so `ld-hook.so` must sit where the patched binary can find it. Launching the patched `demo-app` now executes the payload before the application's entry point, then hands control to the application exactly as before — no environment variables or arguments involved, and nothing changed in the application's own code.

*No corpus evidence — derived from upstream documentation.*
