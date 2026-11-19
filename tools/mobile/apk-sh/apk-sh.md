---
name: "apk-sh"
categories:
  - mobile
homepage: "https://github.com/ax/apk.sh"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-13"
github-repo: "ax/apk.sh"
github-stars: 3861
github-pushed: "2026-01-26"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
github-commits-year: 3
gh-activity-checked: "2026-11-13"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "see repository"
---

# apk-sh

> Bash wrapper that automates the Android APK reverse-engineering loop — pull, decode, rebuild, patch — including split-APK joining and frida-gadget injection without root.

## What it's for

`apk.sh` chains apktool, apksigner, zipalign, aapt and adb into one workflow: pulling an app off a device (joining app bundles/split APKs into a single APK on the way), decoding resources and Dalvik bytecode, rebuilding a modified tree back into a signed APK, renaming the package, and — its headline feature — patching an APK to load `frida-gadget.so` at startup so the app can be instrumented on a non-rooted device. It also supports direct bytecode manipulation without decompilation, which avoids apktool's recompile failure modes on obfuscated apps. It is glue, not an analyzer: it does not decompile Java to source and does not defeat packing/protection by itself. It needs the external tools (apktool ≥ the usual 2.x line, apksigner, adb, aapt, zipalign, unxz) on PATH.

## Typical use cases

### Pull an app from a device

```
./apk.sh pull com.example.target
```

Outcome: pulls the package from the connected device; if it is an app bundle/split APK, the splits are joined into one APK with fixed resource identifiers — a single file ready for patching.

### Decode an APK for editing

```
./apk.sh decode target.apk
./apk.sh decode target.apk -r -s
```

Outcome: apktool-decodes the APK into a directory (smali + resources); `-r` skips resources and `-s` skips dex disassembly for a fast, partial decode when only one side is needed.

### Rebuild and re-sign

```
./apk.sh build target/
```

Outcome: rebuilds the decoded tree into an APK, zipaligns and signs it with apksigner — the file can be reinstalled directly.

### Inject frida-gadget without root

```
./apk.sh patch target.apk --arch arm
adb install target.gadget.apk
```

Outcome: the APK is rebuilt with `frida-gadget.so` loaded on start for the chosen architecture (arm, arm64, x86, x86_64); on launch, the gadget listens on 127.0.0.1:27042 and blocks until `attach()`/`resume()`, giving full Frida instrumentation on an unmodified, non-rooted device.

### Configure the gadget interaction

```
./apk.sh patch target.apk --arch arm --gadget-conf config.json
```

Outcome: the supplied JSON is installed as the gadget configuration — e.g. `script` interaction with `path: /data/local/tmp/script.js` runs an instrumentation script autonomously at startup with no outside connection.

### Rename the package

```
./apk.sh rename target.apk com.example.clone
```

Outcome: the APK's package identity is changed so the instrumented build installs alongside the original app on the same device.

*No corpus evidence — derived from upstream documentation.*
