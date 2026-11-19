---
tool: "stegosuite"
build-system: "java"
upstream: "https://codeberg.org/tob/stegosuite"
recipe: "kali-dsc"
version: "0.9.4-1"
---

# Installing stegosuite

> steganography tool to hide information in image files

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/stegosuite) (version `0.9.4-1`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install stegosuite
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss stegosuite`); otherwise build from source below.

## Any other Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

**Source:** download the release from the [upstream site](https://codeberg.org/tob/stegosuite) (the recipe uses a versioned archive).

```bash
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `default-jdk`, `liblogback-java`, `libmaven-compiler-plugin-java`, `libmaven-jar-plugin-java`, `libslf4j-java`, `libswt-gtk-4-java`, `libpicocli-java`, `maven-debian-helper`
- Runtime deps: see [pkg.kali.org/pkg/stegosuite](https://pkg.kali.org/pkg/stegosuite)

Backlinks: [[stego]] · [[stegosuite]] · [[Kali Gap Analysis]]
