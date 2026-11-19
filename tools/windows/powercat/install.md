---
tool: "powercat"
build-system: "windows"
upstream: "https://github.com/besimorhino/powercat"
recipe: "kali-debian"
version: "0.0~git20240305.4e33fdf-0kali1"
---

# Installing powercat

> netcat features all in powershell v2 This package contains a netcat powershell version. It's a simple utility which reads and writes data across network connections using DNS or UDP protocol.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/powercat) (version `0.0~git20240305.4e33fdf-0kali1`).

## Kali / Debian-based

```bash
sudo apt install powercat
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss powercat`); otherwise build from source below.

## Any other Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/besimorhino/powercat
wine <tool>.exe
```

## Dependencies (from debian/control)

- Runtime (Debian names): `kali-defaults`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[windows]] · [[powercat]] · [[Kali Gap Analysis]]
