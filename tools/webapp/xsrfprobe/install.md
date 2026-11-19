---
tool: "xsrfprobe"
build-system: "python"
upstream: "https://github.com/0xInfection/XSRFProbe"
recipe: "blackarch"
source: "https://github.com/0xInfection/XSRFProbe"
license: "GPL3"
blackarch-pkgver: "590.0dbc5c2"
---

# Installing xsrfprobe

> The Prime Cross Site Request Forgery Audit and Exploitation Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xsrfprobe) (version `590.0dbc5c2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S xsrfprobe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/0xInfection/XSRFProbe.git
cd XSRFProbe
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `build`, `httplib2`, `pip`, `requests`, `stringdist`, `tld`, `urllib3`, `wheel`, `yattag`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xsrfprobe]] · [[BlackArch Tools]]

