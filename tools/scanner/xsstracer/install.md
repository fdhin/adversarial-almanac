---
tool: "xsstracer"
build-system: "scripts"
upstream: "https://github.com/1N3/XSSTracer"
recipe: "blackarch"
source: "https://github.com/1N3/XSSTracer"
license: "custom:unknown"
blackarch-pkgver: "5.f2ed21a"
---

# Installing xsstracer

> Python script that checks remote web servers for Clickjacking, Cross-Frame Scripting, Cross-Site Tracing and Host Header Injection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xsstracer) (version `5.f2ed21a`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S xsstracer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/1N3/XSSTracer.git
cd XSSTracer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[xsstracer]] · [[BlackArch Tools]]

