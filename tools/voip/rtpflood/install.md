---
tool: "rtpflood"
build-system: "other"
upstream: "http://www.hackingvoip.com/sec_tools.html"
recipe: "kali-debian"
version: "1.0-1kali3"
---

# Installing rtpflood

> Tool to flood any RTP device A command line tool used to flood any device that is processing RTP.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/rtpflood) (version `1.0-1kali3`).

## Kali / Debian-based

```bash
sudo apt install rtpflood
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss rtpflood`); otherwise build from source below.

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.hackingvoip.com/sec_tools.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Build dependencies (from source .dsc)

- `debhelper-compat`
- Runtime deps: see [pkg.kali.org/pkg/rtpflood](https://pkg.kali.org/pkg/rtpflood)

Backlinks: [[voip]] · [[rtpflood]] · [[Kali Gap Analysis]]
