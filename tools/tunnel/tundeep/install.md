---
tool: "tundeep"
build-system: "make"
upstream: "https://www.adampalmer.me/iodigitalsec/tundeep/"
recipe: "parrot-debian"
recipe-url: "https://deb.parrot.sh/parrot/pool/main/t/tundeep"
version: "1.1~git20190802-0parrot1"
---

# Installing tundeep

> Layer 2 VPN/injection tool The tool resides [almost] entirely in user space on the victim aside from the pcap requirement.

Recipe source of truth: Parrot's Debian packaging (`.debian.tar.xz` from their pool — full `debian/control` + `debian/rules`).

## Parrot / Debian-based

```bash
curl -fsSL https://deb.parrot.sh/parrot/archive/parrot-keyring.gpg | sudo tee /usr/share/keyrings/parrot.gpg
echo "deb [signed-by=/usr/share/keyrings/parrot.gpg] https://deb.parrot.sh/parrot/ parrot main" | sudo tee /etc/apt/sources.list.d/parrot.list
sudo apt update
sudo apt install tundeep
```

## Arch / BlackArch

Not packaged (checked 2026-11-07) — check the AUR (`paru -Ss tundeep`); otherwise build from source.

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from [upstream site](https://www.adampalmer.me/iodigitalsec/tundeep/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from debian/control)

- Build (Debian names): `zlib1g-dev`, `libpcap-dev`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
override_dh_auto_install:
```

Backlinks: [[tunnel]] · [[tundeep]] · [[Parrot Gap Analysis]]
