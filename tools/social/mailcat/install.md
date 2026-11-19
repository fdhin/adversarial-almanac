---
tool: "mailcat"
build-system: "python"
upstream: "https://github.com/sharsil/mailcat/"
recipe: "blackarch"
source: "https://github.com/sharsil/mailcat/"
license: "Apache-2.0"
blackarch-pkgver: "0.1.1"
---

# Installing mailcat

> The only cat who can find existing email addresses by nickname.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mailcat) (version `0.1.1`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S mailcat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sharsil/mailcat/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `aiohttp-socks`, `aiosmtplib`, `build`, `dnspython`, `installer`, `lxml-html-clean`, `requests-html`, `setuptools`, `tqdm`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "${_pkgname//-/_}-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[mailcat]] · [[BlackArch Tools]]

