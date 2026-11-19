---
tool: "airopy"
build-system: "scripts"
upstream: "https://github.com/Josue87/Airopy"
recipe: "blackarch"
source: "https://github.com/Josue87/Airopy"
license: "custom:unknown"
blackarch-pkgver: "5.b83f11d"
---

# Installing airopy

> Get (wireless) clients and access points.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/airopy) (version `5.b83f11d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S airopy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Josue87/Airopy.git
cd Airopy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scapy`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[airopy]] · [[BlackArch Tools]]

