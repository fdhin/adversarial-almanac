---
tool: "prometheus-firewall"
build-system: "scripts"
upstream: "https://github.com/averagesecurityguy/prometheus"
recipe: "blackarch"
source: "https://github.com/averagesecurityguy/prometheus"
license: "GPL2"
blackarch-pkgver: "176.a316d66"
---

# Installing prometheus-firewall

> A Firewall analyzer written in ruby

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/prometheus-firewall) (version `176.a316d66`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S prometheus-firewall    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/averagesecurityguy/prometheus.git
cd prometheus
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[prometheus-firewall]] · [[BlackArch Tools]]

