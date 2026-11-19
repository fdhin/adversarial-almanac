---
tool: "qsslcaudit"
build-system: "cmake"
upstream: "https://github.com/gremwell/qsslcaudit"
recipe: "kali-debian"
version: "0.8.3-0kali2"
---

# Installing qsslcaudit

> test SSL/TLS clients how secure they are This tool can be used to determine if an application that uses TLS/SSL for its data transfers does this in a secure way.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/qsslcaudit) (version `0.8.3-0kali2`).

## Kali / Debian-based

```bash
sudo apt install qsslcaudit
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss qsslcaudit`); otherwise build from source below.

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/gremwell/qsslcaudit
cd qsslcaudit
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from debian/control)

- Build (Debian names): `cmake`, `libcrypto++-dev`, `libgnutls28-dev`, `libunsafessl-dev`, `qtbase5-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --buildsystem=cmake --sourcedirectory=.
override_dh_auto_configure:
	dh_auto_configure -- -DCMAKE_BUILD_TYPE=Release
```

Backlinks: [[recon]] · [[qsslcaudit]] · [[Kali Gap Analysis]]
