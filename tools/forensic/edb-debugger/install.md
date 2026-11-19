---
tool: "edb-debugger"
build-system: "cmake"
upstream: "http://www.codef00.com/projects#debugger"
recipe: "kali-debian"
aur-package: "edb-debugger"
version: "1.3.0-2.2+b1"
---

# Installing edb-debugger

> cross platform x86/x86-64 debugger edb is a graphical cross platform x86/x86-64 debugger. It was inspired by Ollydbg, but aims to function on x86 and x86-64 as well as multiple OS's. Linux is the only officially supported platform at the moment, but FreeBSD, OpenBSD, OSX and Windows ports are underway with varying degrees of functionality.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/edb-debugger) (version `1.3.0-2.2+b1`).

## Kali / Debian-based

```bash
sudo apt install edb-debugger
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss edb-debugger`); otherwise build from source below.

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](http://www.codef00.com/projects#debugger) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from debian/control)

- Runtime (Debian names): `edb-debugger-plugins`
- Build (Debian names): `cmake`, `libboost-dev`, `libcapstone-dev`, `libdouble-conversion-dev`, `libgraphviz-dev`, `libqt5svg5-dev`, `libqt5xmlpatterns5-dev`, `pkg-config`, `qt5-qmake`, `qtbase5-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[forensic]] · [[edb-debugger]] · [[Kali Gap Analysis]]
