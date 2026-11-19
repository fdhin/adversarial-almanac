---
tool: "ip-tracer"
build-system: "scripts"
upstream: "https://github.com/Rajkumrdusad/IP-Tracer"
recipe: "blackarch"
source: "https://github.com/Rajkumrdusad/IP-Tracer"
license: "MIT"
blackarch-pkgver: "92.6b9c454"
---

# Installing ip-tracer

> Track and retrieve any ip address information.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ip-tracer) (version `92.6b9c454`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ip-tracer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Rajkumrdusad/IP-Tracer.git
cd IP-Tracer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ip-tracer]] · [[BlackArch Tools]]

