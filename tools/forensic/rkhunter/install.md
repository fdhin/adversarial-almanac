---
tool: "rkhunter"
build-system: "perl"
upstream: "http://rkhunter.sourceforge.net/"
recipe: "arch-official/extra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/rkhunter/-/raw/main/PKGBUILD"
version: "1.4.6"
license: "GPL"
---

# Installing rkhunter

> rootkit, backdoor, sniffer and exploit scanner

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/rkhunter/-/raw/main/PKGBUILD) (version `1.4.6`, license GPL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S rkhunter
```

## Kali / Debian-based — same package name

```bash
sudo apt install rkhunter
```

## Any other Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://rkhunter.sourceforge.net) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `perl`
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[rkhunter]] · [[Kali Gap Analysis]]
