---
tool: "jackdaw"
build-system: "scripts"
upstream: "https://github.com/skelsec/jackdaw"
recipe: "blackarch"
source: "https://github.com/skelsec/jackdaw"
license: "custom:unknown"
blackarch-pkgver: "417.19e38d1"
---

# Installing jackdaw

> Collect all information in your domain, show you graphs on how domain objects interact with each-other and how to exploit these interactions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jackdaw) (version `417.19e38d1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jackdaw    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/skelsec/jackdaw) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiosmb`, `asyauth`, `asysocks`, `bidict`, `colorama`, `connexion`, `cryptography`, `flask-sqlalchemy`, `igraph`, `minikerberos`, `msldap`, `networkx`, `pypykatz`, `sqlalchemy`, `swagger-ui-bundle`, `tqdm`, `websockets`, `werkzeug`, `winacl`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[jackdaw]] · [[BlackArch Tools]]

