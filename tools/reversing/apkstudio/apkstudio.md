---
name: "apkstudio"
categories:
  - blackarch-reversing
version: "6.3.0.r0.gc6e2724"
homepage: "http://www.vaibhavpandey.com/apkstudio/"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: cmake
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "vaibhavpandeyvpz/apkstudio"
github-stars: 4744
github-pushed: "2026-10-28"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---

# apkstudio

> Qt6 IDE for decompiling, editing, recompiling, signing and installing Android application packages.

## What it's for

APK Studio is an open-source, cross-platform IDE for reverse-engineering Android APKs. It wraps the apktool decompile/recompile cycle in a real editor — smali syntax highlighting, XML/YAML editing, an image viewer and a built-in hex editor — and can sign rebuilt packages and install them over ADB. Manufacturer framework files (HTC, LG, Samsung, ...) can be installed for decoding vendor APKs, and extra apktool arguments (such as `--force-all`, `--no-res`) can be passed per operation. Java-source browsing comes from the JADX tool it can fetch on first launch; smali editing remains the core workflow. It is a GUI application, not a batch command-line decompiler.

## Typical use cases

### Launch the IDE

Open the APK Studio workspace.

```
apkstudio
```

Outcome: the Qt GUI starts; on first launch it offers to download and configure its tool bundle (Java, Apktool, JADX, ADB, Uber APK Signer), or system installations can be selected in Settings instead.

### Open an APK straight into the decompile dialog

Pass an APK on the command line to skip the file picker.

```
apkstudio target.apk
```

Outcome: the GUI starts with the decompile dialog already showing target.apk selected, ready to decompile it into a project.

### One-time apktool hookup on distro installs

Link the distribution's apktool into APK Studio's vendor directory with the packaged helper.

```
apkstudio-setup.sh
```

Outcome: creates `~/.apkstudio/vendor/` and symlinks the system apktool.jar into it, so decompilation works without letting APK Studio download its own vendor bundle.

*No corpus evidence — derived from upstream documentation.*
