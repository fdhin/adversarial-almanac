---
tool: "awsbucketdump"
build-system: "scripts"
upstream: "https://github.com/jordanpotti/AWSBucketDump"
recipe: "blackarch"
source: "https://github.com/jordanpotti/AWSBucketDump"
license: "MIT"
blackarch-pkgver: "82.4684670"
---

# Installing awsbucketdump

> A tool to quickly enumerate AWS S3 buckets to look for loot.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/awsbucketdump) (version `82.4684670`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S awsbucketdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jordanpotti/AWSBucketDump.git
cd AWSBucketDump
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `xmltodict`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/wordlists`

Backlinks: [[automation]] · [[awsbucketdump]] · [[BlackArch Tools]]

