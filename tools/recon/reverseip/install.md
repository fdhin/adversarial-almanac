---
tool: "reverseip"
build-system: "scripts"
upstream: "https://github.com/lolwaleet/ReverseIP"
recipe: "blackarch"
source: "https://github.com/lolwaleet/ReverseIP"
license: "custom:unknown"
blackarch-pkgver: "13.42cc9c3"
---

# Installing reverseip

> Ruby based reverse IP-lookup tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/reverseip) (version `13.42cc9c3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S reverseip    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lolwaleet/ReverseIP.git
cd ReverseIP
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-colorize`
  - `ruby-mechanize`
  - `ruby-mini_portile2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[reverseip]] · [[BlackArch Tools]]

