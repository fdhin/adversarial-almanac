---
tool: "rdwatool"
build-system: "python"
upstream: "https://github.com/p0dalirius/RDWAtool"
recipe: "blackarch"
nix-package: "rdwatool"
source: "https://github.com/p0dalirius/RDWAtool"
license: "custom:unknown"
blackarch-pkgver: "1.2.r15.g75e6267"
---

# Installing rdwatool

> A python script to extract information from a Microsoft Remote Desktop Web Access (RDWA) application.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rdwatool) (version `1.2.r15.g75e6267`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rdwatool    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/p0dalirius/RDWAtool.git
cd RDWAtool
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `build`, `installer`, `setuptools`, `urllib3`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[rdwatool]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rdwatool    # ad-hoc run, no install
nix profile install nixpkgs#rdwatool   # or persist it
```
