---
tool: "magisk"
build-system: "cpp"
upstream: "https://github.com/topjohnwu/Magisk"
recipe: "upstream"
license: "GPL3"
---

# Installing magisk

> Systemless Android rooting suite: MagiskSU root, modules, Zygisk, and the magiskpolicy/magiskboot/su/resetprop CLI surface.

Source: HackTricks feed admission; upstream [`topjohnwu/Magisk`](https://github.com/topjohnwu/Magisk).

## Prebuilt / releases

GitHub Releases are the only official distribution: grab the Magisk APK from
<https://github.com/topjohnwu/Magisk/releases>. Install the APK on the device
(unlock bootloader first), use **Install → Patch a file** against the device's own
`boot.img`/`init_boot.img`, then flash the patched image with `fastboot flash`.

The CLI binaries (`magiskpolicy`, `magiskboot`, `su`, `resetprop`) are already
present on any rooted device under Magisk's tmpfs path (`magisk --path`); a
standalone `magiskboot` also ships in the source tree's built artifacts.

## On-device (after rooting)

```bash
adb shell
su
magisk --path && magiskpolicy --help
```

## Building from source

Needs the Android SDK/NDK and a JDK; the repo builds with Gradle:

```bash
git clone --recurse-submodules https://github.com/topjohnwu/Magisk.git
cd Magisk
./gradlew assembleRelease
```

Full build requirements: <https://topjohnwu.github.io/Magisk/build.html>.

## Not yet packaged

No mainstream distro packages Magisk (it is an Android app + on-device binaries,
not a host package). Checked 2026-11-13.

Backlinks: [[mobile]] · [[magisk]]
