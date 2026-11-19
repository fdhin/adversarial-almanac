---
tool: "ssl-hostname-resolver"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/120634/Common-Name-Grabber-Script.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/120634/Common-Name-Grabber-Script.html"
license: "GPL3"
blackarch-pkgver: "1"
---

# Installing ssl-hostname-resolver

> CN (Common Name) grabber on X.509 Certificates over HTTPS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssl-hostname-resolver) (version `1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ssl-hostname-resolver    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/120634/Common-Name-Grabber-Script.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-bytes-random-secure`
  - `perl-crypt-ssleay`
  - `perl-net-cidr-lite`
  - `perl-netaddr-ip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ssl-hostname-resolver]] · [[BlackArch Tools]]

