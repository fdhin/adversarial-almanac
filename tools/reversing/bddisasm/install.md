---
tool: "bddisasm"
build-system: "cmake"
upstream: "https://github.com/bitdefender/bddisasm/"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/b/bddisasm"
version: "1.37.0-0parrot1"
---

# Installing bddisasm

> The BitDefender disassembler The Bitdefender disassembler (bddisasm) is a lightweight, x86/x64 only instruction decoder. It is easy to integrate, easy to work with, it has no external dependencies, it

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install bddisasm
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss bddisasm`); otherwise build from source.

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/bitdefender/bddisasm/
cd bddisasm
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from debian/control)

- Build (Debian names): `cmake`, `rapidjson-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@ --buildsystem=cmake
```

Backlinks: [[reversing]] · [[bddisasm]] · [[Parrot Gap Analysis]]
