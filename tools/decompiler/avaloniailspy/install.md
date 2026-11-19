---
tool: "avaloniailspy"
build-system: "other"
upstream: "https://github.com/icsharpcode/AvaloniaILSpy"
recipe: "blackarch"
aur-package: "avaloniailspy"
source: "https://github.com/icsharpcode/AvaloniaILSpy"
license: "MIT"
blackarch-pkgver: "v7.2.rc.r11.g21d4939"
---

# Installing avaloniailspy

> .NET Decompiler (port of ILSpy)

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/avaloniailspy) (version `v7.2.rc.r11.g21d4939`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S avaloniailspy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/icsharpcode/AvaloniaILSpy
cd AvaloniaILSpy
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `dotnet-sdk`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `dotnet-runtime`
  - `libicns`

## Exact build steps from the recipe

```bash
cd $pkgname

  dotnet build --configuration release --output "build"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[avaloniailspy]] · [[BlackArch Tools]]

