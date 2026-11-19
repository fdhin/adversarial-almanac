---
tool: "trusttrees"
build-system: "python"
upstream: "https://github.com/mandatoryprogrammer/TrustTrees"
recipe: "blackarch"
source: "https://github.com/mandatoryprogrammer/TrustTrees"
license: "custom:unknown"
blackarch-pkgver: "102.a9b7399"
---

# Installing trusttrees

> A Tool for DNS Delegation Trust Graphing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/trusttrees) (version `102.a9b7399`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S trusttrees    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mandatoryprogrammer/TrustTrees.git
cd TrustTrees
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `boto3`, `dnsimple`, `dnspython`, `idna`, `pygraphviz`, `requests`, `requests-file`, `setuptools`, `six`, `tldextract`
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

Backlinks: [[recon]] · [[trusttrees]] · [[BlackArch Tools]]

