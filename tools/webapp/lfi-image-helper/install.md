---
tool: "lfi-image-helper"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/129871/LFI-Image-Helper-0.8.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/penetration/LFI_Image_Helper.zip"
license: "custom:unknown"
blackarch-pkgver: "0.8"
---

# Installing lfi-image-helper

> A simple script to infect images with PHP Backdoors for local file inclusion attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lfi-image-helper) (version `0.8`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lfi-image-helper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/penetration/LFI_Image_Helper.zip
tar xf LFI_Image_Helper.zip
cd LFI_Image_Helper
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-color-output`
  - `perl-image-exiftool`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[lfi-image-helper]] · [[BlackArch Tools]]

