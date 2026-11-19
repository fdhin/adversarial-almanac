---
tool: "sh00t"
build-system: "scripts"
upstream: "https://github.com/pavanw3b/sh00t"
recipe: "blackarch"
source: "https://github.com/pavanw3b/sh00t"
license: "MIT"
blackarch-pkgver: "214.8c7321f"
---

# Installing sh00t

> A Testing Environment for Manual Security Testers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sh00t) (version `214.8c7321f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sh00t    # needs the [blackarch] repo enabled
```

## Docker (upstream-recommended deployment)

```bash
docker pull pavanw3b/sh00t:latest
docker volume create --name sh00t
docker run -d -p 8000:8000 --name sh00t -v sh00t:/root/sh00t/db pavanw3b/sh00t:latest
```

Then open http://127.0.0.1:8000/ and log in with `sh00t` / `sh00t`. Restart later with `docker start sh00t`; data persists in the `sh00t` volume.

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pavanw3b/sh00t) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `django-rest-framework`, `django-tables2`, `pytz`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[sh00t]] · [[BlackArch Tools]]

