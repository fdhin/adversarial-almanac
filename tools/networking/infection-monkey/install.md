---
tool: "infection-monkey"
build-system: "scripts"
upstream: "https://github.com/guardicore/monkey"
recipe: "blackarch"
source: "https://github.com/guardicore/monkey"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v2.2.0.r3599.ga55fc8d"
provides: ["monkey-island"]
---

# Installing infection-monkey

> Automated security testing tool for networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/infection-monkey) (version `v2.2.0.r3599.ga55fc8d`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S infection-monkey    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/guardicore/monkey) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `boto3`, `botocore`, `cffi`, `cryptography`, `dateutil`, `dpath`, `ecdsa`, `egg-timer`, `flask`, `flask-jwt-extended`, `flask-pymongo`, `flask-restful`, `gevent`, `jsonschema`, `ldaptor`, `marshmallow`, `marshmallow-enum`, `mongoengine`, `mongomock`, `netifaces`, `odict`, `paramiko`, `psutil`, `pycryptodome`, `pyftpdlib`, `pyjwt`, `pymssql`, `pypsrp`, `pypubsub`, `pypykatz`, `pysmb`, `pyspnego`, `pytest`, `requests`, `requests-mock`, `ring`, `semver`, `serpentarium`, `simplejson`, `six`, `stix2`, `stix2-patterns`, `tqdm`, `urllib3`, `virtualenv`, `werkzeug`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aws-cli`
  - `impacket`
  - `pyinstaller`
  - `pymssql`
  - `python`

## Installed layout (per recipe)

- Commands: `/usr/bin/monkey-island`

Backlinks: [[networking]] · [[infection-monkey]] · [[BlackArch Tools]]

