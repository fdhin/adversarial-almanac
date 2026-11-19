---
tool: "hellraiser"
build-system: "node"
upstream: "https://github.com/m0nad/HellRaiser"
recipe: "blackarch"
source: "https://github.com/m0nad/HellRaiser"
license: "custom:unknown"
blackarch-pkgver: "279.bea43e2"
---

# Installing hellraiser

> Vulnerability Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hellraiser) (version `279.bea43e2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hellraiser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

```bash
git clone https://github.com/m0nad/HellRaiser.git
cd HellRaiser
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `make`, `nodejs`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `nmap`
  - `redis`
  - `ruby-bundler`
  - `ruby-foreman`
  - `ruby-pkg-config`
  - `ruby-rake`
  - `ruby2.7`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[hellraiser]] · [[BlackArch Tools]]

