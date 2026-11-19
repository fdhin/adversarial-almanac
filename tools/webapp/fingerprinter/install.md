---
tool: "fingerprinter"
build-system: "ruby"
upstream: "https://github.com/erwanlr/Fingerprinter"
recipe: "blackarch"
source: "https://github.com/erwanlr/Fingerprinter"
license: "CC-BY-NC-4.0"
blackarch-pkgver: "486.e0078b4"
---

# Installing fingerprinter

> CMS/LMS/Library etc Versions Fingerprinter.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fingerprinter) (version `486.e0078b4`, license CC-BY-NC-4.0).

## Arch / BlackArch

```bash
sudo pacman -S fingerprinter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `ruby`):**

```bash
git clone https://github.com/erwanlr/Fingerprinter.git
cd Fingerprinter
bundle install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`
  - `ruby-pkg-config`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[fingerprinter]] · [[BlackArch Tools]]

