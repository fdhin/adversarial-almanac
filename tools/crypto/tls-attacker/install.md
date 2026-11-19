---
tool: "tls-attacker"
build-system: "java"
upstream: "https://github.com/RUB-NDS/TLS-Attacker"
recipe: "blackarch"
source: "https://github.com/RUB-NDS/TLS-Attacker"
license: "Apache"
blackarch-pkgver: "9469.9a0dc6f5f"
---

# Installing tls-attacker

> A Java-based framework for analyzing TLS libraries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tls-attacker) (version `9469.9a0dc6f5f`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S tls-attacker    # needs the [blackarch] repo enabled
```

## Any Linux distro

Needs a JRE/JDK (`java` 11+ recommended).

```bash
git clone https://github.com/RUB-NDS/TLS-Attacker.git
cd TLS-Attacker
# build per recipe steps below, then run:
java -jar <tool>.jar
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`, `maven`

## Exact build steps from the recipe

```bash
cd $pkgname

  mvn package -DskipTests=true
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[tls-attacker]] · [[BlackArch Tools]]

