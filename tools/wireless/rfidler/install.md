---
tool: "rfidler"
build-system: "python"
upstream: "https://github.com/ApertureLabsLtd/RFIDler"
recipe: "pentoo-ebuild"
version: "0.01_p20150718"
license: "GPL-2"
---

# Installing rfidler

> Software defined RFID (LF) Reader/Writer/Emulator

Recipe source of truth: Pentoo [`rfidler.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/rfidler) (version `0.01_p20150718`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask rfidler
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss rfidler`).

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/ApertureLabsLtd/RFIDler
cd RFIDler
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Python (pip): `matplotlib`, `pyserial`
- Gentoo packages: `dev-embedded/mphidflash`

Backlinks: [[wireless]] · [[rfidler]] · [[Pentoo Gap Analysis]]
