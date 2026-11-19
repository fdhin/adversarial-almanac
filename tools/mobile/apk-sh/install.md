---
tool: "apk-sh"
build-system: "scripts"
upstream: "https://github.com/ax/apk.sh"
recipe: "upstream"
license: "see repository"
---

# Installing apk-sh

> Bash script automating Android APK reverse engineering: pull/decode/build/rename plus frida-gadget patching — no rooted device required.

Source: HackTricks feed admission; upstream [`ax/apk.sh`](https://github.com/ax/apk.sh).

## From source (it's a single script)

```bash
git clone https://github.com/ax/apk.sh.git
cd apk.sh
chmod +x apk.sh
./apk.sh
```

## Requirements (must be on PATH)

- `apktool`
- `apksigner`
- `zipalign`, `aapt`
- `adb`
- `unxz`

On Arch: `pacman -S android-tools apktool android-sdk-build-tools`.
On Debian/Kali: `apt install apktool adb zipalign apksigner aapt`.

## Not yet packaged

No distro carries apk.sh itself (checked 2026-11-13) — clone from GitHub.

Backlinks: [[mobile]] · [[apk-sh]]
