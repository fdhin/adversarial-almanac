---
tool: "suricata-verify"
build-system: "scripts"
upstream: "https://github.com/OISF/suricata-verify"
recipe: "blackarch"
source: "https://github.com/OISF/suricata-verify.git"
license: "custom:unknown"
blackarch-pkgver: "suricata.8.0.8.r0.g5ee5412"
---

# Installing suricata-verify

> Suricata Verification Tests - Testing Suricata Output.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/suricata-verify) (version `suricata.8.0.8.r0.g5ee5412`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S suricata-verify    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/OISF/suricata-verify.git
cd suricata-verify
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `future`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `jq`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[suricata-verify]] · [[BlackArch Tools]]

