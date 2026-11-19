---
tool: "fpicker"
build-system: "make"
upstream: "https://github.com/ttdennis/fpicker"
recipe: "upstream"
license: "MIT"
---

# Installing fpicker

> Frida-based in-process fuzzing suite (AFL++ proxy, standalone active, passive tracing).

Source: HackTricks feed admission; upstream [`ttdennis/fpicker`](https://github.com/ttdennis/fpicker).

## Requirements

- [frida-compile](https://github.com/frida/frida-compile) (npm) to build harness scripts
- `frida-core-devkit` for your platform from
  [Frida releases](https://github.com/frida/frida/releases) — store the library
  and header as `libfrida-core-<platform>.a` / `frida-core-<platform>.h`
  (`macos`, `ios`, or `linux`) next to the Makefile
- For AFL++ mode: [AFL++](https://github.com/AFLplusplus/AFLplusplus/)
  (on macOS/iOS build it with `CFLAGS="-DUSEMMAP=1"`, plus `-DTARGET_OS_IOS` on iOS)

## Build

```bash
git clone https://github.com/ttdennis/fpicker && cd fpicker
make fpicker-linux    # or: make fpicker-macos | make fpicker-ios
```

`update_frida_version.sh` refreshes the pinned Frida version before building.

## Harness

Each target needs a JS harness compiled to one file:

```bash
npm install -g frida-compile   # if not present
frida-compile test.js -o harness.js
```

## Not yet packaged

No distro packages fpicker — build from source. Checked 2026-11-13.

Backlinks: [[fuzzer]] · [[fpicker]]
