---
tool: "see-surf"
build-system: "scripts"
upstream: "https://github.com/In3tinct/See-SURF"
recipe: "blackarch"
source: "https://github.com/In3tinct/See-SURF"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v3.0.r27.g2d15238"
---

# Installing see-surf

> Security tool to find potential vulnerable Server Side Request Forgery (SSRF) parameters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/see-surf) (version `v3.0.r27.g2d15238`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S see-surf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/In3tinct/See-SURF.git
cd See-SURF
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `absl`, `anthropic`, `beautifulsoup4`, `google-genai`, `ollama`, `openai`, `pycryptodome`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[see-surf]] · [[BlackArch Tools]]

