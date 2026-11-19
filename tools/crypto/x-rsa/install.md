---
tool: "x-rsa"
build-system: "scripts"
upstream: "https://github.com/X-Vector/X-RSA"
recipe: "blackarch"
source: "https://github.com/X-Vector/X-RSA"
license: "GPL3"
blackarch-pkgver: "166.291d4c7"
---

# Installing x-rsa

> Contains a many of attack types in RSA such as Hasted, Common Modulus, Chinese Remainder Theorem.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/x-rsa) (version `166.291d4c7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S x-rsa    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/X-Vector/X-RSA.git
cd X-RSA
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `future`, `gmpy2`, `pycryptodome`, `requests`, `sympy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gmp`
  - `libmpc`
  - `mpfr`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[x-rsa]] · [[BlackArch Tools]]

