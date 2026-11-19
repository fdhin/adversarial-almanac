---
name: "mpt"
categories:
  - mobile
homepage: "https://github.com/ByteSnipers/mobile-pentest-toolkit"
github-repo: "ByteSnipers/mobile-pentest-toolkit"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: GPL3
github-stars: 102
github-pushed: "2026-05-15"
github-commits-year: 1
gh-checked: "2026-11-14"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# mpt

> Project-based Android pentest orchestrator — one `mpt` command wires the device, proxy, decompiler, and Frida workflow around the app under test.

## What it's for

MPT (Mobile Pentest Toolkit, ByteSnipers) automates the scaffolding of an Android assessment: per-project folders, tool installation, device management over ADB, proxy/Burp configuration, and the standard dynamic-analysis steps (frida server, SSL-pinning and root-detection bypasses, memory dumps, APK dump-patch-sign). Instead of remembering where twenty tools live and which flags they need, you configure the app once in `~/.mpt/settings.json` and drive everything through `mpt` flags. It bundles or installs the usual suspects (MobSF, objection, RMS, jadx, apktool, dex2jar, fridump, adb, scrcpy, android-backup-extractor) into a managed directory. It orchestrates rather than replaces: each underlying tool is still the reference for its own deep usage. Development is slow (single-digit commits per year), but the workflow surface is stable.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Install the toolchain and start a project

```
mpt --install-tools
mpt --setup app.apk
```

Outcome: the first command installs the supported pentest tools into the configured directory; the second creates the per-app pentest folder, records package name and APK path in `~/.mpt/settings.json`, and sets the stage for every later flag.

### Enumerate device packages

```
mpt --list-packages all
```

Outcome: lists installed applications with APK path and data directory (`all` includes system apps) — pick the target package for the workflow flags.

### Run Frida on the device

```
mpt --frida
```

Outcome: pushes and starts a matching frida-server on the connected rooted device (`--frida-select-version` to pin one); combine with `-s com.example.app` to disable SSL pinning or `-r com.example.app` to disable root detection for the chosen app.

### Work a non-rooted device

```
mpt --setup-no-root
```

Outcome: runs the dump → debug-flag-enable → frida-gadget-patch → repack-and-sign chain on the configured APK, producing an installable build with a Frida gadget and backup/debug allowed — the root-free dynamic-testing path (`-psa` re-runs just the pack-and-sign step after edits).

### Decompile to source

```
mpt --decompile jadx
```

Outcome: launches the chosen decompiler (`jadx` default, `jd-gui` and `luyten` supported) on the configured app for static review; `-D` dumps the running app's memory via fridump instead.

### Point the device at Burp

```
mpt --configure-burp
```

Outcome: embeds the Burp CA certificate into the app's network security config and switches the device Wi-Fi proxy on (`-pe host:port` sets the proxy manually, `-pd` disables it, `-ap` spins up a hotspot suitable for proxying).
