---
tool: "domain-stats"
build-system: "python"
upstream: "https://github.com/MarkBaggett/domain_stats"
recipe: "blackarch"
source: "https://github.com/MarkBaggett/domain_stats"
license: "custom:unknown"
blackarch-pkgver: "169.759c52c"
provides: ["domain_stats", "domain_stats_db_admin"]
---

# Installing domain-stats

> A web API to deliver domain information from whois and alexa.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/domain-stats) (version `169.759c52c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S domain-stats    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MarkBaggett/domain_stats.git
cd domain_stats
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `dateutil`, `munge`, `publicsuffixlist`, `rdap`, `requests`, `setuptools`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/domain_stats`, `/usr/bin/domain_stats_db_admin`

Backlinks: [[recon]] · [[domain-stats]] · [[BlackArch Tools]]

