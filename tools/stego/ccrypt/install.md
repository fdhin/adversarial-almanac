---
tool: "ccrypt"
build-system: "make"
upstream: "https://ccrypt.sourceforge.net/"
recipe: "kali-dsc"
nix-package: "ccrypt"
aur-package: "ccrypt"
version: "1.11-7"
---

# Installing ccrypt

> secure encryption and decryption of files and streams

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/ccrypt) (version `1.11-7`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install ccrypt
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss ccrypt`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#ccrypt    # ad-hoc run, no install
nix profile install nixpkgs#ccrypt   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://ccrypt.sourceforge.net/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `dh-elpa`, `libcrypt-dev`
- Runtime deps: see [pkg.kali.org/pkg/ccrypt](https://pkg.kali.org/pkg/ccrypt)

Backlinks: [[stego]] · [[ccrypt]] · [[Kali Gap Analysis]]
