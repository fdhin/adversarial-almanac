---
tool: "autopwn"
build-system: "python"
upstream: "https://github.com/nccgroup/autopwn"
recipe: "blackarch"
source: "https://github.com/nccgroup/autopwn"
license: "AGPL"
blackarch-pkgver: "190.fc80cef"
---

# Installing autopwn

> Specify targets and run sets of tools against them.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/autopwn) (version `190.fc80cef`, license AGPL).

## Arch / BlackArch

```bash
sudo pacman -S autopwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/autopwn) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `blessings`, `click-shell`, `flask-apscheduler`, `flask-restplus`, `flask-sqlalchemy`, `inquirer`, `pysqlite3`, `readchar`, `requests`, `screenutils`, `setuptools`, `werkzeug`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `arachni`
  - `cmsmap`
  - `dirb`
  - `enum4linux`
  - `httrack`
  - `hydra`
  - `linenum`
  - `linux-exploit-suggester`
  - `lynis`
  - `metasploit`
  - `nbtscan`
  - `nfs-utils`
  - `nikto`
  - `nmap`
  - `openssh`
  - `patator`
  - `python`
  - `skipfish`
  - `sslscan`
  - `sslyze`
  - `testssl.sh`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[autopwn]] · [[BlackArch Tools]]

