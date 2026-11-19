---
tool: "carwhisperer"
build-system: "perl"
upstream: "https://trifinite.org/stuff/carwhisperer/"
recipe: "blackarch"
source: "https://trifinite.org/stuff/carwhisperer/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.2"
---

# Installing carwhisperer

> Sensibilise manufacturers of carkits and other Bluetooth appliances without display and keyboard for the possible security threat evolving from the use of standard passkeys.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/carwhisperer) (version `0.2`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S carwhisperer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://trifinite.org/stuff/carwhisperer/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[carwhisperer]] · [[BlackArch Tools]]

