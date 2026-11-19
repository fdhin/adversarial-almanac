---
tool: "spray365"
build-system: "scripts"
upstream: "https://github.com/MarkoH17/Spray365"
recipe: "blackarch"
source: "https://github.com/MarkoH17/Spray365"
license: "MIT"
blackarch-pkgver: "0.2.2.beta.r1.g22b39af"
---

# Installing spray365

> Makes spraying Microsoft accounts (Office 365 / Azure AD) easy through its customizable two-step password spraying approach.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spray365) (version `0.2.2.beta.r1.g22b39af`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S spray365    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/MarkoH17/Spray365.git
cd Spray365
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `click-option-group`, `colorama`, `msal`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[spray365]] · [[BlackArch Tools]]

