---
tool: "serializationdumper"
build-system: "scripts"
upstream: "https://github.com/NickstaDB/SerializationDumper/"
recipe: "blackarch"
source: "https://github.com/NickstaDB/SerializationDumper/"
license: "MIT"
blackarch-pkgver: "34.6d161cd"
---

# Installing serializationdumper

> A tool to dump Java serialization streams in a more human readable form.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/serializationdumper) (version `34.6d161cd`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S serializationdumper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/NickstaDB/SerializationDumper.git
cd SerializationDumper
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `java-runtime`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[serializationdumper]] · [[BlackArch Tools]]

