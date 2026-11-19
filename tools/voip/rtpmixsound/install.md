---
tool: "rtpmixsound"
build-system: "make"
upstream: "http://www.hackingvoip.com/sec_tools.html"
recipe: "kali-debian"
version: "3.0-1kali5+b1"
---

# Installing rtpmixsound

> Mixes pre-recorded audio in real-time A tool to mix pre-recorded audio in real-time with the audio (i.e. RTP) in the specified target audio stream.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/rtpmixsound) (version `3.0-1kali5+b1`).

## Kali / Debian-based

```bash
sudo apt install rtpmixsound
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss rtpmixsound`); otherwise build from source below.

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.hackingvoip.com/sec_tools.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from debian/control)

- Runtime (Debian names): `libfindrtp`
- Build (Debian names): `libfindrtp`, `libnet1-dev`, `libpcap-dev`

Backlinks: [[voip]] · [[rtpmixsound]] · [[Kali Gap Analysis]]
