---
name: "bagbak"
categories:
  - blackarch-mobile
version: "342.71fbd4d"
homepage: "https://github.com/ChiChou/bagbak"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: node
license: Apache
meta-checked: "2026-11-09"
github-repo: "ChiChou/bagbak"
github-stars: 1511
github-pushed: "2026-09-29"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 28
gh-activity-checked: "2026-11-09"
---

# bagbak

> Dump a decrypted IPA — or just chosen binaries — from a jailbroken iOS device over Frida.

## What it's for

bagbak is a Node.js command-line decryptor for App Store apps: it attaches Frida to the target app on a jailbroken iOS device, locates the decrypted slices of the app's Mach-O binaries in memory, and rebuilds an installable `.ipa` (or a zip of the decrypted binaries) for static analysis. It needs frida-server running on the device — bagbak 5 requires frida 17; with a frida 16 server use bagbak 4 — and connects over USB by default or to a remote frida-server. The upstream README now marks the run-the-app decryption approach deprecated in favor of `mremap_encrypted`-based tools (UnFairPlay, unfaird), which decrypt without executing the target app; bagbak remains the straightforward Frida route.

## Typical use cases

### List apps on the connected device

```
bagbak -l
```

Outcome: prints the installed apps discovered through Frida (bundle IDs and names) so an exact dump target can be chosen; add `-j` for the same list as JSON.

### Dump a full decrypted IPA

```
bagbak com.example.app
```

Outcome: decrypts every binary in the app and writes `com.example.app-[version].ipa` into the working directory.

### Dump only selected binaries

```
bagbak com.example.app main
```

Outcome: decrypts just the app's main executable — faster when only the primary binary will be analyzed; the other dump modes are `extensions` (app-extension binaries only) and `binaries` (all executables as a zip).

### Strip Info.plist restrictions while dumping

```
bagbak --remove-keys UISupportedDevices com.example.app
```

Outcome: the dumped IPA's Info.plist omits the listed keys (here the device-model whitelist), so it installs on any device; multiple keys are comma-separated.

*No corpus evidence — derived from upstream documentation.*
