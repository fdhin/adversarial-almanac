---
tool: "villain"
build-system: "scripts"
upstream: "https://github.com/t3l3machus/Villain"
recipe: "blackarch"
nix-package: "villain"
source: "https://github.com/t3l3machus/Villain"
license: "CCBY-NC-ND-4.0"
blackarch-pkgver: "V2.2.1.r3.g87e5dd1"
---

# Installing villain

> C2 framework that can handle multiple TCP socket & HoaxShell-based reverse shells, enhance their functionality with additional features and share them among connected sibling servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/villain) (version `V2.2.1.r3.g87e5dd1`, license CCBY-NC-ND-4.0).

## Arch / BlackArch

```bash
sudo pacman -S villain    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/t3l3machus/Villain.git
cd Villain
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `gnureadline`, `netifaces`, `pycryptodome`, `pycryptodomex`, `pyperclip`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[villain]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#villain    # ad-hoc run, no install
nix profile install nixpkgs#villain   # or persist it
```
