---
tool: "wpa-sycophant"
build-system: "make"
upstream: "https://github.com/sensepost/wpa_sycophant"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/w/wpa-sycophant"
version: "1.0+git20210103-0parrot1"
---

# Installing wpa-sycophant

> tool to relay phase 2 authentication attempts to access corporate wireless This package contains a tool to relay phase 2 authentication attempts to access corporate wireless without cracking the passw

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install wpa-sycophant
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss wpa-sycophant`); otherwise build from source.

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/sensepost/wpa_sycophant
cd wpa_sycophant
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from debian/control)

- Build (Debian names): `libnl-genl-3-dev`, `libssl-dev`, `libnl-3-dev`, `pkg-config`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_build:
	make -C wpa_supplicant
```

Backlinks: [[wireless]] · [[wpa-sycophant]] · [[Parrot Gap Analysis]]
