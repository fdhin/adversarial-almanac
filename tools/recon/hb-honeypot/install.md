---
tool: "hb-honeypot"
build-system: "scripts"
upstream: "https://packetstormsecurity.com/files/126068/Heartbleed-Honeypot-Script.html"
recipe: "kali-debian"
nix-package: "hb-honeypot"
version: "0.1.1-1kali5"
---

# Installing hb-honeypot

> Heartbleed Honeypot Script This Perl script listens on TCP port 443 and responds with completely bogus SSL heartbeat responses, unless it detects the start of a byte pattern similar to that used in Jared Stafford's (jspenguin@jspenguin.org) demo for CVE-2014-0160 'Heartbleed'. Run as root for the privileged port. Outputs IPs of suspected heartbleed scan to the console. Rickrolls scanner in the hex dump.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/hb-honeypot) (version `0.1.1-1kali5`).

## Kali / Debian-based

```bash
sudo apt install hb-honeypot
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss hb-honeypot`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#hb-honeypot    # ad-hoc run, no install
nix profile install nixpkgs#hb-honeypot   # or persist it
```

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://packetstormsecurity.com/files/126068/Heartbleed-Honeypot-Script.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Runtime (Debian names): `perl`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[recon]] · [[hb-honeypot]] · [[Kali Gap Analysis]]
