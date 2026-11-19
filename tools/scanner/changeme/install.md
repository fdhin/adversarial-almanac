---
tool: "changeme"
build-system: "scripts"
upstream: "https://github.com/ztgrace/changeme"
recipe: "blackarch"
source: "https://github.com/ztgrace/changeme"
license: "GPL3"
blackarch-pkgver: "272.26fa2c3"
---

# Installing changeme

> A default credential scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/changeme) (version `272.26fa2c3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S changeme    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ztgrace/changeme) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cerberus`, `jinja`, `logutils`, `lxml`, `memcached`, `mock`, `netaddr`, `nose`, `paramiko`, `pymongo`, `pyodbc`, `pysnmp`, `python-libnmap`, `redis`, `requests`, `schema`, `selenium`, `shodan`, `sqlalchemy`, `tabulate`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[scanner]] · [[changeme]] · [[BlackArch Tools]]

