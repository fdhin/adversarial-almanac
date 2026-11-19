---
tool: "routerkeygenpc"
build-system: "cmake"
upstream: "https://github.com/routerkeygen/routerkeygenPC"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/r/routerkeygenpc"
version: "1.1.0+git20190721.c1f1665-0parrot1"
---

# Installing routerkeygenpc

> Router Keygen generate default WPA/WEP keys This package generates default WPA/WEP keys for the several routers:  * Thomson based routers ( this includes Thomson, SpeedTouch, Orange,    Infinitum, BBo

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install routerkeygenpc
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss routerkeygenpc`); otherwise build from source.

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/routerkeygen/routerkeygenPC
cd routerkeygenPC
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from debian/control)

- Build (Debian names): `cmake`, `libssl-dev`, `qtscript5-dev`, `qttools5-dev`, `qttools5-dev-tools`, `libnm-dev`, `libglib2.0-dev`, `pkg-config`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_configure:
	dh_auto_configure -- -DCMAKE_BUILD_TYPE=Release
override_dh_auto_build:
	cd cli && qmake && make
```

Backlinks: [[wireless]] · [[routerkeygenpc]] · [[Parrot Gap Analysis]]
