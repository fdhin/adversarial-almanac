---
tool: "csrftester"
build-system: "scripts"
upstream: "http://www.owasp.org/index.php/Category:OWASP_CSRFTester_Project"
recipe: "blackarch"
source: "http://www.owasp.org/index.php/Category:OWASP_CSRFTester_Project"
license: "LGPL"
blackarch-pkgver: "1.0"
---

# Installing csrftester

> The OWASP CSRFTester Project attempts to give developers the ability to test their applications for CSRF flaws.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/csrftester) (version `1.0`, license LGPL).

## Arch / BlackArch

```bash
sudo pacman -S csrftester    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.owasp.org/index.php/Category:OWASP_CSRFTester_Project) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[csrftester]] · [[BlackArch Tools]]

