---
tool: "threat-dragon"
build-system: "scripts"
upstream: "https://owasp.org/www-project-threat-dragon/"
recipe: "blackarch"
source: "https://owasp.org/www-project-threat-dragon/"
license: "Apache-2.0"
blackarch-pkgver: "2.2.0"
---

# Installing threat-dragon

> Electron Threat Modelling and diagramming tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/threat-dragon) (version `2.2.0`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S threat-dragon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://owasp.org/www-project-threat-dragon/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `alsa-lib` — Debian libasound2-dev / Fedora alsa-lib-devel
  - `cairo`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `libxkbcommon`
  - `nspr`
  - `nss` — Debian libnss3-dev / Fedora nss-devel
  - `pango`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/icons`

Backlinks: [[threat-model]] · [[threat-dragon]] · [[BlackArch Tools]]

