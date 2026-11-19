---
tool: "rshack"
build-system: "scripts"
upstream: "https://github.com/zweisamkeit/RSHack"
recipe: "blackarch"
source: "https://github.com/zweisamkeit/RSHack"
license: "MIT"
blackarch-pkgver: "64.cf197e3"
---

# Installing rshack

> Python tool which allows to carry out some attacks on RSA, and offer a few tools to manipulate RSA keys.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rshack) (version `64.cf197e3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rshack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/zweisamkeit/RSHack.git
cd RSHack
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cryptography`, `gmpy2`, `pycryptodome`, `rsa`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[rshack]] · [[BlackArch Tools]]

