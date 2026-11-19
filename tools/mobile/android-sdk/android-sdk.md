---
name: "android-sdk"
categories:
  - blackarch-mobile
version: "26.1.1"
homepage: "https://developer.android.com/studio/releases/sdk-tools.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2017-11-18"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# android-sdk

> Google's Android SDK command-line tools package — `sdkmanager`, `avdmanager` and the SDK directory layout under `/opt/android-sdk` that Android build, debug and analysis tooling builds upon.

## What it's for

This package installs Google's Android SDK command-line tools (the 26.1.1 sdk-tools distribution) under `/opt/android-sdk`, with `/etc/profile.d/android-sdk.sh` making the tools available on login shells. Its central component is `sdkmanager`, which downloads and manages SDK components — platform-tools (adb, fastboot), build-tools (aapt, aapt2, zipalign, apksigner), platform JARs and emulator system images — into the SDK tree, and `avdmanager`, which creates the Android Virtual Devices the emulator boots. Most other Android tooling assumes an SDK install exists; this package is that substrate, not an analysis tool itself. Two honesty notes: this is the last standalone "tools" package generation Google shipped (2017-era, before the current cmdline-tools distribution that ships via Android Studio), and the bundled Java `sdkmanager` predates Google's current repository layout — when it fails against today's component index, install the current cmdline-tools release instead rather than debugging the old client.

### List installed and available SDK components

See what the SDK knows about before installing anything.

```
/opt/android-sdk/tools/bin/sdkmanager --list
```

Outcome: prints installed packages followed by everything available in Google's repository (platform-tools, build-tools versions, platforms, system images); run it after `source /etc/profile.d/android-sdk.sh` to use plain `sdkmanager`. If the listing errors out, the 26.1.1-era client is talking to a repository that has moved on — fetch the current cmdline-tools.

### Install SDK components

Pull in the pieces the rest of your toolchain needs.

```
/opt/android-sdk/tools/bin/sdkmanager --install "platform-tools"
```

Outcome: downloads and unpacks platform-tools (adb, fastboot) into `/opt/android-sdk/platform-tools/`; other components follow the same quoted-package form, e.g. `"build-tools;33.0.3"` or `"platforms;android-33"`. Run `sdkmanager --licenses` once to accept license terms, and `sdkmanager --update` to refresh installed packages.

### Create an Android Virtual Device

Provision an emulator image to boot.

```
/opt/android-sdk/tools/bin/avdmanager create avd -n testavd -k "system-images;android-30;google_apis;x86_64"
```

Outcome: defines an AVD named `testavd` backed by the given system image, after that image has been installed with `sdkmanager`; list existing AVDs with `avdmanager list avd` and boot one with the `emulator` command from the separate emulator package. The create step prompts for a custom hardware profile unless you accept the default.

*No corpus evidence — derived from upstream documentation.*
