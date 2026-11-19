---
tool: "sooty"
build-system: "scripts"
upstream: "https://github.com/TheresAFewConors/Sooty"
recipe: "blackarch"
source: "https://github.com/TheresAFewConors/Sooty"
license: "custom:unknown"
blackarch-pkgver: "335.6236fd1"
---

# Installing sooty

> The SOC Analysts all-in-one CLI tool to automate and speed up workflow.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sooty) (version `335.6236fd1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sooty    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/TheresAFewConors/Sooty.git
cd Sooty
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `asn1crypto`, `certifi`, `cffi`, `chardet`, `cryptography`, `dnspython`, `idna`, `ipwhois`, `pycparser`, `pyopenssl`, `pysocks`, `requests`, `six`, `strictyaml`, `unfurl`, `urllib3`, `wget`, `win_inet_pton`, `wincertstore`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `tk`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[sooty]] · [[BlackArch Tools]]

