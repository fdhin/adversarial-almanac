---
tool: "cpp2il"
build-system: "other"
upstream: "https://github.com/SamboyCoding/Cpp2IL"
recipe: "blackarch"
source: "https://github.com/SamboyCoding/Cpp2IL"
license: "MIT"
blackarch-pkgver: "2022.1.0.pre.release.21.r150.gb5ad444b"
---

# Installing cpp2il

> A tool to reverse unity's IL2PP toolchain

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cpp2il) (version `2022.1.0.pre.release.21.r150.gb5ad444b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cpp2il    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SamboyCoding/Cpp2IL.git
cd Cpp2IL
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `dotnet-sdk`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `dotnet-host`
  - `dotnet-runtime-6.0`

## Exact build steps from the recipe

```bash
cd $pkgname

  dotnet restore
  dotnet build /p:Configuration=Release --no-restore
  pushd ./Cpp2IL || exit 1
  # It's build name named "ubuntu" but it is suitable for all linux
  # distributions, as long as it is x86_64
  dotnet publish /p:Configuration=Release /p:TargetFramework=net6.0 \
    --no-restore -r ubuntu-x64
  popd || exit 1
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[cpp2il]] · [[BlackArch Tools]]

