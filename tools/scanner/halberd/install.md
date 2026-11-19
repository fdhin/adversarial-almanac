---
tool: "halberd"
build-system: "python"
upstream: "http://halberd.superadditive.com/"
recipe: "blackarch"
source: "http://halberd.superadditive.com/"
license: "GPL"
blackarch-pkgver: "0.2.4"
---

# Installing halberd

> Halberd discovers HTTP load balancers. It is useful for web application security auditing and for load balancer configuration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/halberd) (version `0.2.4`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S halberd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://halberd.superadditive.com/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python2 setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[scanner]] · [[halberd]] · [[BlackArch Tools]]

