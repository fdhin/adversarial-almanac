---
tool: "hak5-wifi-coconut"
build-system: "cmake"
upstream: "https://hak5.org"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/h/hak5-wifi-coconut"
version: "1.1.0-0parrot1"
---

# Installing hak5-wifi-coconut

> Userspace driver for the Hak5 Wi-Fi Coconut Userspace drive for USB Wi-Fi NICs and the Hak5 Wi-Fi Coconut

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install hak5-wifi-coconut
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss hak5-wifi-coconut`); otherwise build from source.

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from [upstream site](https://hak5.org) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from debian/control)

- Runtime (Debian names): `firmware-misc-nonfree`
- Build (Debian names): `cmake`, `pkg-config`, `libusb-1.0-0-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --buildsystem=cmake
```

Backlinks: [[wireless]] · [[hak5-wifi-coconut]] · [[Parrot Gap Analysis]]
