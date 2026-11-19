---
technology: iOS application and device security surface
kind: overview
audience: offensive-security
status: upstream-only
family: mobile
layer: iOS, IPA artifacts, keychain and sandbox model
homepage: https://developer.apple.com/documentation/security
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave 2026-11-13; keychain attribute semantics checked against Apple's Security framework documentation (kSecAttrAccessible item-attribute family) during the wave; zero CVEs cited (the unit documents technique surface, not version-gated bugs)"
source: "six2dez/pentest-book (mobile/ios.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# iOS application and device security surface

> Apple encrypts every App Store binary and sandboxes every app — so the iOS workflow is a chain of acquisitions: get the IPA, get it decrypted, get on the device, and only then start reading the keychain everyone told you was safe.

Companion area page: [[mobile]].

## What it is

An iOS app ships as an IPA — a zip whose payload is `Payload/App.app`: the Mach-O binary, `Info.plist`, resources, embedded provisioning data, and frameworks. Two Apple defaults shape everything an operator does. First, App Store binaries are **FairPlay-encrypted** — the `cryptid` flag in the Mach-O load commands marks the encrypted regions, and static analysis of an unmodified store download reads ciphertext. Every real workflow therefore starts with a *decrypted* binary, which in practice means a device: either a jailbroken phone (dump from memory at runtime) or re-signing a patched artifact you control. Second, each app runs in a **container sandbox** with its data split between the bundle directory and per-app data containers — but the **keychain**, the system credential store, is shared infrastructure with its own (frequently weaker) access rules, and it is where the loot concentrates.

The operator takeaway: the iOS method is a ladder — artifact, decryption, device, instrumentation — and the decisive preparation question is always which rung you start on: a jailbroken device makes everything below it easy, and its absence makes everything above it mandatory.

## The anatomy that matters on an engagement

    acquisition
      App Store IPA (encrypted) ── or ── decrypted dump from device
    static
      Payload/App.app
        main binary ── cryptid? PIE? stack canary? → otool battery
        Info.plist ── ATS exceptions, URL schemes, background modes
        entitlements / provisioning profile ── what the app is allowed
        PrivacyInfo.xcprivacy ── declared data use
    device
      /private/var/mobile/Containers/Data/Application/{HASH}/ ── data container
      /private/var/containers/Bundle/Application/{HASH}/ ── app bundle
      keychain (securityd) ── passwords, tokens, certs — cross-app by access group
      /var/mobile/Library/Preferences/com.apple.wifi.plist ── known-network history
    instrumentation
      patched IPA (Frida gadget) ── clean devices
      frida-server via Cydia/Sileo ── jailbroken devices

Three structural properties drive most findings: (1) **encryption stops at the store** — the moment a binary runs on a device you control, the decrypted image exists and is dumpable; (2) **the keychain outlives app deletations and crosses app boundaries** when access groups allow it — items written with weak accessibility attributes are loot for anything that can run code in a holder's context; (3) **plist culture leaks** — iOS apps persist state to property lists and SQLite with the same discipline as everyone else's config files, which is to say almost none.

## IPA acquisition and decryption

Without a device: ipatool authenticates to the App Store with an Apple ID and downloads the (still-encrypted) package plus its purchase metadata — good for obtaining the artifact, useless for the binary itself until decrypted. With a jailbroken device, frida-ios-dump is the standard answer — it spawns the app and pulls the decrypted image from memory. Manual route: locate the bundle directory under `/var/containers/Bundle/Application/`, copy the `.app` tree out over SSH, re-zip as `Payload/`-rooted IPA if needed. Generic static triage (MobSF takes IPAs; class-dump style tools read headers) works on the decrypted product.

Re-signing is the workhorse of clean-device work: take the decrypted IPA, inject a Frida gadget, and re-sign with a certificate you control —

```bash
objection patchipa --source app.ipa --codesign-signature "Apple Development: you@example.com"
ios-deploy --bundle Payload/App.app        # sideload the patched build
objection explore                          # attach to the running gadget
```

This needs a valid Apple development identity on the Mac driving it; free accounts work with their provisioning constraints. Entitlement changes ride the same path — a re-signed build can carry amended entitlements, which is both an operator technique and a finding class (what did the app's *real* entitlements allow?).

## Static analysis of the binary

The otool battery on the decrypted Mach-O, in the order that matters:

```bash
otool -l App | grep -A4 LC_ENCRYPTION_INFO   # cryptid 1 = still encrypted
otool -hv App | grep PIE                     # position-independent?
otool -Iv App | grep stack_chk               # stack canaries linked in?
otool -L App                                  # linked libraries (frameworks, static-analysis shortcuts)
```

`cryptid` non-zero means stop and fix acquisition; missing PIE or canaries are hardening findings, and the linked-library list tells you instantly whether the app is Swift, Objective-C, or a hybrid, and which crypto/network stacks to aim hooks at. class-dump (Objective-C) and dsdump/Swift-dump tools regenerate interface headers — on a decrypted binary, all class and method names are back, and iOS method names (`fetchAuthTokenForUser:`, `shouldBypassJailbreakCheck`) do a lot of your reading for you. Run `strings` greps for `api|key|secret|password|token` against the binary before anything fancier.

`Info.plist` is the manifest-equivalent confession: ATS exceptions (`NSAppTransportSecurity` with `NSAllowsArbitraryLoads` — cleartext invited), registered URL schemes (`CFBundleURLSchemes` — your deep-link surface), background modes (`UIBackgroundModes` — what the app may do unseen), and on current releases the privacy manifest (`PrivacyInfo.xcprivacy`) declaring data use and required-reason API usage — a map of what the app touches, written by the developer, for you.

## Filesystem loot

On a jailbroken phone (or via runtime file access on a patched build), sweep the data container:

```bash
find /var/mobile/Containers/Data/Application/ -name "*.db" -o -name "*.sqlite"
find /var/mobile/Containers/Data/Application/ -name "*.plist"
ls -la <container>/Library/Caches/ <container>/tmp/
```

High-yield targets: unencrypted SQLite databases (chat logs, caches of API responses), plists and NSUserDefaults state (`objection`'s `ios nsuserdefaults get` reads it in one shot), cookies and caches under `Library/`, and **Snapshots** — `Library/SplashBoard/Snapshots/` holds the system's screenshots of the app in recent states, taken on backgrounding, with whatever was on screen at the time. Outside the container, `com.apple.wifi.plist` in the mobile user's Preferences carries the known-networks history — SSIDs and BSSIDs the device has joined, a physical-world location trail that plays well in reports. Snapshot and Wi-Fi history both survive casual "we deleted the app" cleanup.

## Keychain

The keychain is the system credential store served by `securityd`; items carry service/account attributes, an **access group** (which apps may read the item — default restricts to the writing app's group, shared keychain access groups are opt-in), and an **accessibility** attribute deciding when the item is decryptable:

| kSecAttrAccessible value | Operator read |
|---|---|
| `WhenUnlocked` (+`ThisDeviceOnly` variants) | Item only usable with device unlocked; the defensible choice |
| `AfterFirstUnlock` | Usable after first unlock until reboot — background-refresh-friendly, weaker |
| `Always` (and legacy variants) | Usable whenever — the "bad" grade: readable by any code running in the app, anytime |

Extraction paths: objection's `ios keychain dump` (optionally `--json`) from inside the running app; keychain-dumper on a jailbroken phone dumps the whole store reachable under the mobile user. Access-group misconfigurations are the structural finding — an item written into a widely-shared group (or with no effective group protection) is readable by every app in that group, and third-party SDKs that share keychain groups across *different vendors' apps* turn "encrypted in the keychain" into "shared with strangers".

## Instrumentation

With a gadget or server in place, the objection command set is the daily driver: class and method enumeration (`ios hooking list classes`, `... list class_methods`), watching traffic-shaping methods (`ios hooking watch method "ClassName selector" --dump-args --dump-return`), `ios sslpinning disable` for pinning, `ios cookies get`, `ios plist cat <path>` for arbitrary plists, and `memory search "password" --string` for strings alive in memory. Raw Frida fills the gaps — spawn/attach with `frida -U -f com.example.app`, ObjC-resolver scripts that hook every `URLSession:didReceiveChallenge:` implementation for pinning bypasses, one-liners to invoke URL schemes programmatically (`UIApplication.openURL`), and clipboard monitoring against `UIPasteboard` for apps that copy sensitive values and never clear them. On jailbroken devices, SSL Kill Switch 2 is the set-and-forget unpinning toggle, and Cycript-style runtime exploration still works for quick questions. Log capture (`idevicesyslog`) during scripted app usage exposes the verbose-logging finding class on iOS just as logcat does on Android.

## The jailbreak lane, and detection

Jailbreak tooling clusters by hardware era: the checkm8 bootrom exploit family (checkra1n, palera1n) covers older SoCs broadly; current-generation tooling (the Dopamine line) covers recent iOS versions on exploitable ranges; the honest planning fact is that recent flagship hardware on the newest iOS often has **no public jailbreak**, which is exactly why the patched-IPA lane exists. On a jailbroken phone: the default mobile superuser password is `alpine` (change it on any device you keep), SSH in, install Frida from the build.frida.re repository, and work.

Apps detect jailbreaks by file existence (`/Applications/Cydia.app`, `/bin/bash`, substrate paths), URL-scheme registration checks, sandbox escapes around `fork`, and dylib presence. Countermeasures, in escalation order: objection's `ios jailbreak disable` (hooks the common checks — enough for casual detection), Liberty Lite / A-Bypass tweaks from Cydia repositories (systematic per-app hiding), and hand-written Frida hooks for bespoke checks found in the decompiled code. Symmetric to Android's Play Integrity: iOS integrity signals flow through App Store attestation and device-check APIs, so a server that *asks* for attestation is a harder problem than a client that checks for Cydia.

## Current-iOS posture notes

Recent releases add friction on three fronts that change methodology rather than invalidate it. Lockdown Mode (opt-in hardening) blocks most message attachment types, disables JIT for Safari content, and cuts wired data while locked — devices in this mode are telling you the user is a hard target, and app-level testing still proceeds normally. Privacy manifests and required-reason APIs make apps *declare* fingerprinting-relevant API use — which turns the developer's own declarations into your recon data. And per-app networking controls plus stricter App Transport Security enforcement narrow the trivial-interception window; the ATS exceptions and pinning bypass work in this page remain the working method. Re-verify jailbreak availability for your exact device/iOS pair before promising a device lane on an engagement.

## Where to go next

- [[mobile-android]] — the sibling artifact workflow
- [[mobile]] — the area router (lanes and dispatch)
