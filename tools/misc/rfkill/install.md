---
tool: "rfkill"
build-system: "autotools"
upstream: "https://github.com/util-linux/util-linux"
recipe: "kali-dsc"
version: "2.42.4-1"
---

# Installing rfkill

> tool for enabling and disabling wireless devices

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/rfkill) (version `2.42.4-1`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install rfkill
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss rfkill`); otherwise build from source below.

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/util-linux/util-linux
cd util-linux
./configure
make -j$(nproc)
sudo make install
```

## Build dependencies (from source .dsc)

- `debhelper`, `debhelper-compat`, `dh-exec`, `dh-package-notes`, `dh-sequence-zz-debputy-rrr`, `asciidoctor`, `bc`, `bison`, `flex`, `gettext`, `libaudit-dev`, `libcap-ng-dev`, `libcrypt-dev`, `libcryptsetup-dev`
- Runtime deps: see [pkg.kali.org/pkg/rfkill](https://pkg.kali.org/pkg/rfkill)

Backlinks: [[misc]] · [[rfkill]] · [[Kali Gap Analysis]]
