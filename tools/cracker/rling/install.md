---
tool: "rling"
build-system: "make"
upstream: "https://github.com/Cynosureprime/rling"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/r/rling"
version: "0~git20230629.ec27d46-0parrot1"
---

# Installing rling

> better rli This package is similar to the rli utility found in hashcat-utils, but much, much faster. rli compares a single file against another file(s) and removes all duplicates.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install rling
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss rling`); otherwise build from source.

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/Cynosureprime/rling
cd rling
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from debian/control)

- Build (Debian names): `libjudy-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[cracker]] · [[rling]] · [[Parrot Gap Analysis]]
