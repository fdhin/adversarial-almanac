---
tool: "social-mapper"
build-system: "scripts"
upstream: "https://github.com/SpiderLabs/social_mapper"
recipe: "blackarch"
source: "https://github.com/SpiderLabs/social_mapper"
license: "GPL"
blackarch-pkgver: "190.92be8da"
---

# Installing social-mapper

> A social media enumeration and correlation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/social-mapper) (version `190.92be8da`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S social-mapper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SpiderLabs/social_mapper.git
cd social_mapper
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `django`, `dlib`, `easyprocess`, `face-recognition`, `face-recognition-models`, `facebook-sdk`, `matplotlib`, `numpy`, `pyvirtualdisplay`, `requests`, `scikit-image`, `scikit-learn`, `scipy`, `selenium`, `tabulate`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cookiejar`
  - `ipython`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[social-mapper]] · [[BlackArch Tools]]

