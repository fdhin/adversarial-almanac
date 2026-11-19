---
tool: "jwt-tool"
build-system: "scripts"
upstream: "https://github.com/ticarpi/jwt_tool"
recipe: "blackarch"
source: "https://github.com/ticarpi/jwt_tool"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v2.3.0.r0.g3bc7407"
---

# Installing jwt-tool

> Toolkit for validating, forging and cracking JWTs (JSON Web Tokens).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jwt-tool) (version `v2.3.0.r0.g3bc7407`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S jwt-tool    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ticarpi/jwt_tool.git
cd jwt_tool
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cprint`, `pycryptodomex`, `ratelimit`, `requests`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[jwt-tool]] · [[BlackArch Tools]]

