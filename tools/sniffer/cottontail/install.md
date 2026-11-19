---
tool: "cottontail"
build-system: "python"
upstream: "https://github.com/QKaiser/cottontail"
recipe: "blackarch"
source: "https://github.com/QKaiser/cottontail"
license: "BSD"
blackarch-pkgver: "93.b7f5222"
---

# Installing cottontail

> Capture all RabbitMQ messages being sent through a broker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cottontail) (version `93.b7f5222`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S cottontail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/QKaiser/cottontail) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `coloredlogs`, `pika`, `requests`, `setuptools`, `urllib3`, `verboselogs`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[cottontail]] · [[BlackArch Tools]]

