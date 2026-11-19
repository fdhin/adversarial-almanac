---
tool: "exe2image"
build-system: "scripts"
upstream: "https://github.com/OsandaMalith/Exe2Image"
recipe: "blackarch"
source: "https://github.com/OsandaMalith/Exe2Image/releases/download/1/Exe2Image.7z"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing exe2image

> A simple utility to convert EXE files to JPEG images and vice versa.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/exe2image) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S exe2image    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/OsandaMalith/Exe2Image/releases/download/1/Exe2Image.7z
cd Exe2Image.7z
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[exe2image]] · [[BlackArch Tools]]

