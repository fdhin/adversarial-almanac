---
tool: "ssrf-proxy"
build-system: "ruby"
upstream: "https://github.com/bcoles/ssrf_proxy"
recipe: "blackarch"
source: "https://github.com/bcoles/ssrf_proxy"
license: "MIT"
blackarch-pkgver: "293.e79da7a"
---

# Installing ssrf-proxy

> Facilitates tunneling HTTP communications through servers vulnerable to Server-Side Request Forgery.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssrf-proxy) (version `293.e79da7a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ssrf-proxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

```bash
git clone https://github.com/bcoles/ssrf_proxy.git
cd ssrf_proxy
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`
  - `ruby-rdoc`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[ssrf-proxy]] · [[BlackArch Tools]]

