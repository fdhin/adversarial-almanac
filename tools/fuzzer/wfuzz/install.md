---
tool: "wfuzz"
build-system: "scripts"
upstream: "https://github.com/xmendez/wfuzz"
recipe: "blackarch"
nix-package: "wfuzz"
aur-package: "wfuzz"
source: "https://github.com/xmendez/wfuzz.git"
license: "GPL2"
blackarch-pkgver: "1157.2263cd0"
kali-package: "wfuzz"
kali-version: "3.1.1-1"
---

# Installing wfuzz

> Utility to bruteforce web applications to find their not linked resources.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wfuzz) (version `1157.2263cd0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S wfuzz    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install wfuzz    # Kali package version 3.1.1-1
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/xmendez/wfuzz.git
cd wfuzz
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `chardet`, `future`, `lxml`, `pycurl`, `pyparsing`, `six`, `wxpython`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/wfuzz`

Backlinks: [[fuzzer]] · [[wfuzz]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#wfuzz    # ad-hoc run, no install
nix profile install nixpkgs#wfuzz   # or persist it
```
