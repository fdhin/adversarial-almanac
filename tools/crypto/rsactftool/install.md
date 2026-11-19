---
tool: "rsactftool"
build-system: "scripts"
upstream: "https://github.com/Ganapati/RsaCtfTool"
recipe: "blackarch"
source: "https://github.com/Ganapati/RsaCtfTool"
license: "custom:unknown"
blackarch-pkgver: "695.4ffadb0"
---

# Installing rsactftool

> RSA tool for ctf - retrieve private key from weak public key and/or uncipher data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rsactftool) (version `695.4ffadb0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rsactftool    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Ganapati/RsaCtfTool.git
cd RsaCtfTool
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `bitarray`, `cryptography`, `egcd`, `gmpy2`, `psutil`, `pycryptodome`, `requests`, `six`, `sympy`, `tqdm`, `urllib3`, `z3-solver`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[rsactftool]] · [[BlackArch Tools]]

