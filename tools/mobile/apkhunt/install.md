---
tool: "apkhunt"
build-system: "scripts"
upstream: "https://github.com/Cyber-Buddy/apkhunt"
recipe: "blackarch"
source: "https://github.com/Cyber-Buddy/apkhunt"
license: "GPL-3.0-only"
blackarch-pkgver: "2.0.0.r0.gd753172"
---

# Installing apkhunt

> Static code analysis tool for Android apps that is based on the OWASP MASVS framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apkhunt) (version `2.0.0.r0.gd753172`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S apkhunt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Cyber-Buddy/apkhunt) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `flask`, `lxml`, `requests`, `weasyprint`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `android-apktool`
  - `android-tools`
  - `ca-certificates`
  - `curl`
  - `gunicorn`
  - `jadx`
  - `java-runtime-headless`
  - `python`
  - `unzip`
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[apkhunt]] · [[BlackArch Tools]]

