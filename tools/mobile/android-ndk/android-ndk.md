---
name: "android-ndk"
categories:
  - blackarch-mobile
version: "r29"
homepage: "https://developer.android.com/ndk/downloads"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-11-24"
build-system: scripts
license: GPL, LGPL, custom
meta-checked: "2026-11-09"
github-repo: "android-ndk/ndk"
github-stars: 2327
github-pushed: "2026-09-08"
github-archived: false
github-issues: 134
github-match: "search"
gh-checked: "2026-11-09"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# android-ndk

> Google's Android Native Development Kit: the Clang-based C/C++ cross-toolchain, platform headers and the `ndk-build` make driver for compiling native code into the JNI shared libraries an APK loads.

## What it's for

The NDK is the official toolkit for the native side of Android apps: a Clang-based cross-compiler with sysroots and headers for each Android API level and ABI (arm64-v8a, armeabi-v7a, x86_64, x86), plus `ndk-build`, a Make wrapper that reads `Android.mk`/`Application.mk` files and produces the `.so` shared libraries that get packaged into an APK and loaded from Java/Kotlin via `System.loadLibrary`. The BlackArch package unpacks the official Google release under `/opt/android-ndk` and installs `/etc/profile.d/android-ndk.sh` that puts the NDK on your `PATH` (re-login or `source /etc/profile.d/android-ndk.sh`). For security work the NDK matters mainly as the toolchain for compiling native code — exploit payloads, Frida-style hooks of your own, library rewrites — for Android targets, and as the reference for the `.so` files found inside APKs. It is a build kit, not an analysis tool: it does not decompile or audit existing binaries, and its debugger integration targets your own builds. `ndk-build` remains supported but current upstream guidance for new projects is CMake driven by the NDK toolchain file.

### Build a JNI project with ndk-build

Compile an existing native project laid out the classic way.

```
cd project/ && ndk-build
```

Outcome: reads `project/jni/Android.mk` (module list, sources, flags) and `jni/Application.mk` (target ABIs, API level), compiles and links each native module, and writes the results to `project/libs/<abi>/lib*.so` ready to be packaged into the APK; object files land under `project/obj/`. Re-running rebuilds only what changed, `ndk-build clean` removes the artifacts.

### Drive ndk-build from a nonstandard layout

Build native sources that are not arranged as `<project>/jni/`.

```
ndk-build NDK_PROJECT_PATH=. APP_BUILD_SCRIPT=./Android.mk NDK_APPLICATION_MK=./Application.mk
```

Outcome: builds the modules described by `./Android.mk` using `./Application.mk` for ABI and API-level settings, with `obj/` and `libs/` created in the current directory — the standard way to invoke ndk-build from a standalone native source tree (also what Android Studio's deprecated plugin support used internally).

### Build for every ABI

Cross-compile the same native code for all supported architectures in one go.

```
cd project/ && ndk-build APP_ABI=all
```

Outcome: emits `libs/arm64-v8a/`, `libs/armeabi-v7a/`, `libs/x86_64/` and `libs/x86/` variants of each module instead of only the default `armeabi-v7a`; other `Application.mk` variables accept command-line overrides the same way, e.g. `ndk-build APP_PLATFORM=android-24` to build against the Android 24 platform.

*No corpus evidence — derived from upstream documentation.*
