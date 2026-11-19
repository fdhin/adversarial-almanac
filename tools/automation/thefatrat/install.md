---
tool: "thefatrat"
build-system: "scripts"
upstream: "https://github.com/Screetsec/TheFatRat"
recipe: "blackarch"
source: "https://github.com/Screetsec/TheFatRat"
license: "GPL3"
blackarch-pkgver: "813.b0586d0"
---

# Installing thefatrat

> TheFatRat a massive exploiting tool: easy tool to generate backdoor and easy tool to post exploitation attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/thefatrat) (version `813.b0586d0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S thefatrat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Screetsec/TheFatRat.git
cd TheFatRat
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `names`
- **Build tools**: `gcc`, `git`, `mono`
- **System libraries** (Arch name — translate for your distro):
  - `android-apktool`
  - `android-sdk-build-tools`
  - `apache`
  - `backdoor-factory`
  - `bind-tools`
  - `dex2jar`
  - `exploitdb`
  - `gnome-terminal`
  - `imagemagick` — Debian imagemagick / Fedora ImageMagick
  - `jdk8-openjdk`
  - `jre8-openjdk`
  - `lib32-zlib`
  - `lsb-release`
  - `metasploit`
  - `mingw-w64-gcc`
  - `ncurses`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`
  - `python2`
  - `ruby-nokogiri`
  - `smali`
  - `sudo`
  - `unzip`
  - `upx`
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[thefatrat]] · [[BlackArch Tools]]

