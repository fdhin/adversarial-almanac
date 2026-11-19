---
tool: "missionplanner"
build-system: "scripts"
upstream: "https://firmware.ardupilot.org/Tools/MissionPlanner/"
recipe: "blackarch"
source: "https://firmware.ardupilot.org/Tools/MissionPlanner/"
license: "GPL3"
blackarch-pkgver: "1.3.83"
---

# Installing missionplanner

> A GroundControl Station for Ardupilot.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/missionplanner) (version `1.3.83`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S missionplanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://firmware.ardupilot.org/Tools/MissionPlanner/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `mono`
- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[drone]] · [[missionplanner]] · [[BlackArch Tools]]

