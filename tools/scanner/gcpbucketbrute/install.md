---
tool: "gcpbucketbrute"
build-system: "scripts"
upstream: "https://github.com/RhinoSecurityLabs/GCPBucketBrute"
recipe: "blackarch"
source: "https://github.com/RhinoSecurityLabs/GCPBucketBrute"
license: "MIT"
blackarch-pkgver: "17.6866bd2"
---

# Installing gcpbucketbrute

> A script to enumerate Google Storage buckets, determine what access you have to them, and determine if they can be privilege escalated.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gcpbucketbrute) (version `17.6866bd2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gcpbucketbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/RhinoSecurityLabs/GCPBucketBrute.git
cd GCPBucketBrute
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `google-cloud-storage`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[gcpbucketbrute]] · [[BlackArch Tools]]

