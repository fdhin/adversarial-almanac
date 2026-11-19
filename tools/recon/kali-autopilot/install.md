---
tool: "kali-autopilot"
build-system: "scripts"
upstream: "https://gitlab.com/kalilinux/packages/kali-autopilot"
recipe: "kali-debian"
version: "3.4-0kali3"
---

# Installing kali-autopilot

> tool for automatic attack scripts in Kali Kali Autopilot is a tool to help develop automatic attack scripts  for red and purple teaming. . It is primarily intended to create scripts that attack vulnerable machines in the Kali Purple platform for detection and response training but it is also useful for creating scripts used for penetration testing.

Recipe source of truth: Kali's [`debian/` packaging](https://gitlab.com/kalilinux/packages/kali-autopilot) (version `3.4-0kali3`).

## Kali / Debian-based

```bash
sudo apt install kali-autopilot
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss kali-autopilot`); otherwise build from source below.

## Any other Linux distro

**From source:**

```bash
git clone https://gitlab.com/kalilinux/packages/kali-autopilot
cd kali-autopilot
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from debian/control)

- Python (pip): `easygui`, `sarge`, `wxgtk4.0`, `pymetasploit3`
- Runtime (Debian names): `python3:any`

## Key build steps (from debian/rules)

```make
#!/usr/bin/make -f
	dh $@
```

Backlinks: [[recon]] · [[kali-autopilot]] · [[Kali Gap Analysis]]
