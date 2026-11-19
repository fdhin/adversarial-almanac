---
technology: Mobile attack surface (Android + iOS)
kind: overview
audience: offensive-security
status: upstream-only
family: mobile
layer: mobile operating systems, apps, and managed fleets
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave 2026-11-13; router synthesized from the pentest-book mobile pages plus platform behavior verified against developer.android.com behavior-change and manifest documentation during the wave"
source: "six2dez/pentest-book (mobile/general.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# Mobile attack surface (router)

> Mobile work is two different jobs wearing one name: attacking a *device* you can reach, and auditing an *app* you were handed — the first question decides which tools leave the bag.

## The engagement's first two questions

Every mobile engagement starts with two dispatch decisions, and both must be settled before any tooling choice:

1. **Which lane is this?** Device-on-engage (a handset is reachable or in hand), app-analysis (an artifact — APK or IPA — is the target), or MDM (an enterprise fleet and its management plane are the target).
2. **Which platform?** Android and iOS share almost no tooling below the interception layer; the split decides the page you work from — [[mobile-android]] or [[mobile-ios]].

| Dispatch marker | Lane | Start at |
|---|---|---|
| A handset on the network with `adb` reachable (`5555/tcp`) or USB access | Device-on-engage | [[adbd]] for the daemon surface, then the platform page |
| "Here is our app, test it" — an APK/IPA file, a store listing, a rebuild request | App analysis | [[mobile-android]] / [[mobile-ios]] analysis workflow |
| Managed corporate fleet, enrollment profiles, container apps, wipe/lock policies | MDM posture | Platform page's managed-device sections; the MDM console itself is a web attack surface in its own right |

The lanes overlap late: app analysis usually graduates to a device (emulator, rooted handset, jailbroken phone) for dynamic work, and device access is wasted without the app-analysis skill of reading what runs there. But they start differently — a reachable `adb` daemon is a network-service finding, while a handed-over APK is a code-review engagement with a phone attached.

## What the platforms share

Below the platform split, four things are common enough to plan around once:

- **One static-analysis frame covers both.** Mobile Security Framework (MobSF) ingests APKs and IPAs alike and produces the same class of findings — hardcoded secrets, insecure storage, weak transport configuration — before any manual work starts. Treat its output as a triage map, not a verdict.
- **Interception is the universal first obstacle.** Burp Suite or mitmproxy between the app and its API requires a CA the app trusts *and* surviving certificate pinning. Both platforms now ship defaults that make "install proxy CA and go" fail: Android apps targeting 7.0+ ignore user-installed CAs by default, and iOS requires explicit per-CA trust enabling. The platform pages carry the per-OS workaround matrix (system-store push, config patch, runtime unpinning).
- **Runtime instrumentation converges on one toolchain.** Frida is the instrumentation substrate on both platforms; objection wraps it into an exploration CLI with per-OS command sets (`android sslpinning disable` / `ios sslpinning disable`). The rooted/jailbroken path runs a server on the device; the clean-device path patches the app artifact to embed a Frida gadget.
- **OWASP MASTG/MASVS is the shared checklist language.** When scope or reporting needs categories, both platform pages map their findings to the MASVS domains (storage, crypto, auth, network, platform, code, resilience) rather than inventing a local taxonomy.

## Reading the platforms' minds

Five platform behaviors predict how a mobile target will respond to standard technique, whatever the lane:

- **Android publishes its attack surface.** The manifest inside every APK states which components are exported, which permissions guard them, and which deep-link schemes the app claims. Recon is a decompile away, not a fuzz away.
- **iOS encrypts its artifact by default.** App Store binaries arrive FairPlay-encrypted; analysis begins with acquiring a *decrypted* binary, which shapes the entire iOS workflow (device-mediated dumping or patched re-signing).
- **Both platforms fight instrumentation.** Root detection, jailbreak detection, integrity APIs (Play Integrity on Android), and pinning are defensive defaults in finance-class apps; assume they are present and budget for the bypass work.
- **Recent OS versions keep moving the floor.** Android raised its minimum-target-SDK install gates (legacy apps simply refuse to install on current devices) and tightened background execution; iOS added Lockdown Mode and privacy manifests. Old playbooks age — re-check the current-version sections of each page before quoting technique.
- **Managed devices are double targets.** An MDM profile constrains the user *and* creates a management channel (enrollment, configuration pushes) whose server side is often softer than the device it governs.

## Where to go next

- [[mobile-android]] — APK analysis workflow, exported-component attacks, instrumentation surface
- [[mobile-ios]] — IPA analysis, keychain and filesystem loot, jailbreak-detection bypass
- [[adbd]] — the Android debug daemon as a network-exposed service
