---
name: "ideviceinstaller"
gentoo-category: "app-pda"
version: "1.1.0"
homepage: "http://www.libimobiledevice.org/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo, aur, nixpkgs]
build-system: autotools
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "libimobiledevice/ideviceinstaller"
github-stars: 1472
github-pushed: "2025-10-30"
github-archived: false
github-issues: 67
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ideviceinstaller

> Install, list, and remove apps on an iOS device from the desktop command line.

## What it's for

ideviceinstaller is the libimobiledevice project's CLI for the app installation
service of an iOS device: it enumerates, installs, upgrades and uninstalls apps
by talking to the device's `installation_proxy` over usbmuxd — no iTunes or
other Apple tooling required. It accepts `.ipa` packages, carrier bundles and
developer `.app` directories, and can emit its listings in structured formats
for scripting. Pairing (trust) between host and device is handled by the
libimobiledevice stack underneath it.

## Typical use cases

### List installed apps

Get the bundle identifiers a device holds.

```
ideviceinstaller list
```

Outcome: prints one `<appid>` (bundle identifier) per installed app — the
identifiers that `uninstall` and the archive operations take as input.

### Install an app package

Push an IPA onto the device.

```
ideviceinstaller install target.ipa
```

Outcome: transfers the package to the device through the installation proxy
with a progress indicator; the app appears in the device's launcher.

### Remove an app

Uninstall by bundle identifier.

```
ideviceinstaller uninstall com.example.app
```

Outcome: the app with that bundle identifier is removed from the device;
`ideviceinstaller --help` and `man ideviceinstaller` document the remaining
options (archives, upgrades, output formats).

*No corpus evidence — derived from upstream documentation.*
