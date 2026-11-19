---
tool: "pwnagotchi"
build-system: "python"
upstream: "https://github.com/evilsocket/pwnagotchi"
recipe: "upstream"
license: "GPL3"
---

# Installing pwnagotchi

> A2C reinforcement-learning WiFi "pet" for the Raspberry Pi Zero W that drives bettercap to capture crackable WPA handshakes.

Source: upstream [`evilsocket/pwnagotchi`](https://github.com/evilsocket/pwnagotchi). This is an appliance image, not a package — it ships as a ready-to-flash release for the RPi0W (plus supported e-ink display hats). Full setup guide: [pwnagotchi.ai](https://pwnagotchi.ai/).

## Flash the release image

1. Download the latest `.img` from [upstream releases](https://github.com/evilsocket/pwnagotchi/releases/latest).
2. Write it to a microSD card:

```bash
unxz -k pwnagotchi-<version>.img.xz
sudo dd if=pwnagotchi-<version>.img of=/dev/sdX bs=4M conv=fsync status=progress
```

3. Optional: drop a `config.toml` on the boot partition to override defaults (name, `ui.web` settings, personality parameters — the factory values live in `pwnagotchi/defaults.toml` in the repo).
4. Boot the Pi on power-only USB for AUTO/AI operation, or connect the data port to a host for MANUAL mode.

## Not packaged by distros

No distro packages pwnagotchi — it is consumed as the prebuilt image above. The Python sources in the repo build the unit's daemon and are only useful for custom images and development.

Backlinks: [[wireless]] · [[pwnagotchi]]
