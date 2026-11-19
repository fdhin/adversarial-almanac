---
name: "apk-mitm"
categories:
  - mobile
homepage: "https://github.com/niklashigi/apk-mitm"
origin: "hacktricks"
recipe: "upstream"
packaged-by: [aur]
build-system: node
license: MIT
meta-checked: "2026-11-13"
github-repo: "niklashigi/apk-mitm"
github-match: "exact"
github-stars: 5137
gh-checked: "2026-11-13"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# apk-mitm

> One-command APK patcher that makes Android apps proxy-friendly — rewrites the network security config and disables certificate pinning without root.

## What it's for

apk-mitm automates preparing an Android APK for HTTPS inspection: given an APK it decodes it with Apktool, replaces the app's Network Security Configuration so user-added (proxy) certificates are trusted, patches the code to disable various certificate-pinning implementations, rebuilds and signs the result with uber-apk-signer — no rooted device required. It also handles Android App Bundle formats (`.xapk` from APKPure, `.apks` exported with SAI). Its known limits: Apktool decode/encode failures on large or complex apps, and pinning performed inside native binaries (e.g. Flutter apps) is effectively impossible to patch this way — for those, runtime hooking tools (mitmproxy's android-unpinner, HTTP Toolkit's frida-interception-and-unpinning scripts) are the stronger approach, at the cost of usually needing a rooted device.

## Typical use cases

### Patch an APK for proxying

The basic flow before any mobile traffic capture.

```
apk-mitm example.apk
```

Outcome: prints the decode → modify manifest → replace network security config → disable pinning → encode → sign steps and leaves `./example-patched.apk`; install that build on the device, point its Wi-Fi proxy at Burp/mitmproxy, and the app's HTTPS becomes inspectable.

### Pause for manual edits

Change something the automation cannot.

```
apk-mitm example.apk --wait
```

Outcome: the run stops before re-encoding, leaving the decoded app in a temporary directory for hand edits — the documented use is replacing a certificate-restricted Google Maps API key in `AndroidManifest.xml` — and continues to build the patched APK when released.

### Patch an app bundle

Apps distributed as split bundles.

```
apk-mitm example.xapk
```

Outcome: accepts `.xapk` (e.g. from APKPure) and `.apks` (exported with SAI) bundle files and produces a patched, installable result — on Linux this additionally requires `zip` and `unzip` to be installed.

### Bake the proxy CA into the app

Devices where adding a user certificate is impossible.

```
apk-mitm example.apk --certificate proxy-ca.pem
```

Outcome: the given `.pem`/`.der` certificate is added directly to the app's Network Security Config as a trusted CA — the workaround for devices like Android TVs that offer no UI for user-installed certificates.

### Swap the bundled Apktool

Work around decode/encode crashes.

```
apk-mitm example.apk --apktool /path/to/apktool.jar
```

Outcome: uses the specified Apktool JAR instead of the bundled version — when patching crashes during decode or encode, pinning a known-good Apktool release (check their issue tracker) is the documented remedy.

No corpus evidence — derived from upstream documentation.
