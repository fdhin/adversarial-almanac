---
name: "testssl-install"
categories:
  - scanner
---

# Installing testssl

Bash script with bundled helper binaries — cloning the repo is the canonical install:

```bash
git clone --depth 1 https://github.com/testssl/testssl.sh
cd testssl.sh
./testssl.sh target.example.com
```

Default branch is `3.3dev`; checkout the latest stable tag (`git tag -l`) for a fixed release. Requires bash (v3+), awk/sed/tr and an OpenSSL or LibreSSL binary (statically linked ones ship in `./bin/`).

## Distro packages

```bash
sudo pacman -S testssl.sh      # Arch (also in Debian/Kali as testssl.sh)
```

## Docker

```bash
docker run --rm -ti drwetter/testssl.sh target.example.com
```

Note: published Docker images lag the 3.3dev branch — after the November 2026 move of the project to the `testssl` organization, clone from the org repo above, not the emptied `drwetter/testssl.sh` slug.

Verification: `./testssl.sh -b` prints the banner with version and the OpenSSL binary in use.
