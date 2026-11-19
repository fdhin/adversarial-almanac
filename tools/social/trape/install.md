---
tool: "trape"
build-system: "scripts"
upstream: "https://github.com/jofpin/trape"
recipe: "blackarch"
source: "https://github.com/jofpin/trape"
license: "custom:unknown"
blackarch-pkgver: "132.6baae24"
---

# Installing trape

> People tracker on the Internet: OSINT analysis and research tool by Jose Pino.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/trape) (version `132.6baae24`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S trape    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jofpin/trape) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `engineio`, `eventlet`, `flask`, `flask-cors`, `flask-login`, `flask-socketio`, `itsdangerous`, `jinja`, `markupsafe`, `requests`, `six`, `socketio`, `werkzeug`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[trape]] · [[BlackArch Tools]]

