---
tool: "php-rfi-payload-decoder"
build-system: "scripts"
upstream: "https://github.com/bwall/PHP-RFI-Payload-Decoder"
recipe: "blackarch"
source: "https://github.com/bwall/PHP-RFI-Payload-Decoder"
license: "BSD"
blackarch-pkgver: "30.bd42caa"
---

# Installing php-rfi-payload-decoder

> Decode and analyze RFI payloads developed in PHP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/php-rfi-payload-decoder) (version `30.bd42caa`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S php-rfi-payload-decoder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/bwall/PHP-RFI-Payload-Decoder.git
cd PHP-RFI-Payload-Decoder
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `apache`
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[php-rfi-payload-decoder]] · [[BlackArch Tools]]

