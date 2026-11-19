---
tool: "githubcloner"
build-system: "scripts"
upstream: "https://github.com/mazen160/GithubCloner"
recipe: "blackarch"
source: "https://github.com/mazen160/GithubCloner"
license: "MIT"
blackarch-pkgver: "36.20203e7"
---

# Installing githubcloner

> A script that clones Github repositories of users and organizations automatically.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/githubcloner) (version `36.20203e7`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S githubcloner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mazen160/GithubCloner.git
cd GithubCloner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `gitpython`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[githubcloner]] · [[BlackArch Tools]]

