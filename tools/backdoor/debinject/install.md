---
tool: "debinject"
build-system: "scripts"
upstream: "https://github.com/UndeadSec/Debinject"
recipe: "blackarch"
source: "https://github.com/UndeadSec/Debinject"
license: "BSD3"
blackarch-pkgver: "43.d884309"
---

# Installing debinject

> Inject malicious code into *.debs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/debinject) (version `43.d884309`, license BSD3).

## Arch / BlackArch

```bash
sudo pacman -S debinject    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/UndeadSec/Debinject.git
cd Debinject
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[debinject]] · [[BlackArch Tools]]

