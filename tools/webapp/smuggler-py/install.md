---
tool: "smuggler-py"
build-system: "scripts"
upstream: "https://github.com/gwen001/pentest-tools/blob/master/smuggler.py"
recipe: "blackarch"
source: "https://github.com/gwen001/pentest-tools/blob/master/smuggler.py"
license: "custom:IDBIL"
blackarch-pkgver: "v2.0.0.r2.ge4b0dfa"
---

# Installing smuggler-py

> Python tool used to test for HTTP Desync/Request Smuggling attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smuggler-py) (version `v2.0.0.r2.ge4b0dfa`, license custom:IDBIL).

## Arch / BlackArch

```bash
sudo pacman -S smuggler-py    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/gwen001/pentest-tools.git
cd pentest-tools
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colored`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[smuggler-py]] · [[BlackArch Tools]]

