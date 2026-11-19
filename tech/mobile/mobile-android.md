---
technology: Android application and device security surface
kind: overview
audience: offensive-security
status: upstream-only
family: mobile
layer: Android OS, APK artifacts, IPC model
homepage: https://developer.android.com
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave 2026-11-13; component-export semantics and manifest defaults verified against developer.android.com manifest documentation, OS hardening facts checked against the Android 12/14/15 behavior-change pages during the wave; zero CVEs cited (the unit documents technique surface, not version-gated bugs)"
source: "six2dez/pentest-book (mobile/android.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# Android application and device security surface

> The manifest ships inside every APK and lists, in cleartext, exactly which doors the app left open to other apps — the attack surface of an Android target is largely a published document you only have to decompile and read.

Companion area page: [[mobile]].

## What it is

An Android app is delivered as an APK: a manifest (`AndroidManifest.xml`) declaring the app's components, permissions, and hardware/software requirements; compiled Dalvik/ART bytecode (classes.dex) that jadx-class tools turn back into readable Java; native libraries under `lib/` per architecture; and resources — including `resources.arsc` and the XML string tables where developers are told to keep their strings, and routinely keep their secrets.

The security model that matters to an attacker has three layers. First, **IPC by declaration**: activities, services, broadcast receivers, and content providers are the app's doors to other apps, and whether each door is `exported` is (mostly) a manifest fact, discoverable statically. Second, **permission gating**: both install-time and runtime permissions, plus custom permissions whose `protectionLevel` decides who may hold them. Third, **data isolation**: each app gets a UID and a private `/data/data/<package>/` sandbox — but backup settings, debug flags, external storage, and exported components each punch holes an operator can walk through.

The operator takeaway: an unusually large share of Android findings are *configuration findings* — a component exported that should not be, a permission too permissive, a backup allowed, cleartext permitted — and they fall out of a disciplined read of one XML file before any dynamic work begins.

## The anatomy that matters on an engagement

    APK → decompile
      AndroidManifest.xml ── components (activity/service/receiver/provider)
                            ── exported flags + intent filters → deep links
                            ── permissions declared & custom (protectionLevel)
                            ── allowBackup / debuggable / networkSecurityConfig
      classes.dex → jadx → readable Java/Kotlin ── secrets, crypto, logic
      resources.arsc + res/values/strings.xml ── API keys, endpoints
      lib/<abi>/*.so ── native code (Flutter/RN hide logic here)
    device side
      /data/data/<package>/ ── app-private storage (needs root or run-as)
      /storage/emulated/0/Android/{data,obb}/<package>/ ── world-readable external
      adb (USB or TCP 5555) ── see [[adbd]] for the daemon as a service target

Three structural properties drive most findings: (1) **exported components are callable by any app on the device** — a malicious app, a proof-of-concept harness you write, or `adb shell am`/`pm` invocations; (2) **the manifest's word is the truth** — if it says exported, the code's intent does not matter; (3) **the string tables are a confession** — developers externalize "temporary" keys, internal hostnames, and test endpoints into resources that ship to every phone.

## The APK analysis workflow

### Acquire

When the app is not handed over directly, pull it from a device you control or from third-party Play-mirror storefronts (APKPure, APKCombo, and similar mirrors serve raw APKs without a device). From a device:

```bash
adb shell pm list packages            # inventory
adb shell pm path com.vendor.app      # split-APK paths
adb pull <each-split-path> .          # modern apps ship as several .apk splits
```

Splits must be recombined (e.g. with an APK-builder tool) for single-file tooling; MobSF and jadx accept directories/zip forms.

### Decompile

```bash
jadx -d out/ --deobf app.apk          # bytecode → Java, names deobfuscated
apktool d app.apk                     # manifest/resources decoded to XML
aapt dump badging app.apk             # package, version, targetSdk in one line
```

jadx answers "what does the code do"; apktool answers "what exactly does the manifest and resource tree say". `aapt dump badging` is the 10-second fingerprint: package name, version, and the `targetSdkVersion` that decides which OS hardening applies.

### Sweep for secrets and endpoints

Automated: apkleaks and APKEnum run the URL/secret regex pass over an APK; slicer highlights quick-win patterns in an unpacked tree; APKHunt runs a manifest/static checklist; MobSF's web UI gives the same class of findings plus dynamic hooks. Manual, over the apktool output:

```bash
grep -EHirn "accesskey|admin|api_?key|checkClientTrusted|password|pinning|secret|SharedPreferences|token|X509" .
grep -Phro "(https?://)[\w\./-]+" out/ | sort -u | grep -v "schemas.android\|w3.org"
```

Two patterns worth their own greps: Google API keys (`AIza[0-9A-Za-z_\-]{35}`) and FCM server keys (`AAAA[A-Za-z0-9_-]{7}:[A-Za-z0-9_-]{140}`) — an FCM server key found in a binary is push-notification control, not just a string. When `strings.xml` or the decompiled constants contain a `*.firebaseio.com` host, try fetching `https://<project>.firebaseio.com/.json` — misconfigured Firebase real-time databases answer with the whole database.

### Manifest triage table

| Marker | Why it matters |
|---|---|
| `android:allowBackup="true"` | `adb backup` (on devices that still honor it) extracts the app's private data without root |
| `android:debuggable="true"` | Any shell user can `run-as` the package and read its private storage |
| exported components with intent filters | Callable by any other app; see the next section |
| custom permissions without `protectionLevel="signature"` | Any app can request the permission that gates a "protected" component |
| `WRITE/READ_EXTERNAL_STORAGE` on apps that handle secrets | Secrets can land on world-readable external storage |
| cleartext permitted / no `networkSecurityConfig` | Downgrade and interception room |

Also read the permission request against actual behavior: an app that only opens the browser but declares `INTERNET` plus broad storage permissions is over-privileged — dangerous capability with no user-visible justification.

## Exported-component attacks

Ground rules (verified against the platform's manifest documentation): a component with **no** intent filter defaults to `exported="false"`; the moment a component declares an **intent filter**, the effective default is exported — and since the Android 12 target level, apps must state `android:exported` explicitly for such components or the package will not install. Practically: every `<activity>`, `<service>`, `<receiver>`, `<provider>` carrying an `<intent-filter>` is in scope, stated or defaulted.

**Activities and deep links.** An exported activity is launchable by any app or by `adb shell am start`. Test it with attacker-chosen extras: one exported activity that forwards a received intent into a protected action turns the whole app into an intent proxy. Deep links compound this — a custom `scheme://` handler with unvalidated host/path/parameter handling reproduces the web bug classes inside the app: open redirects (unverified hosts), injection when parameters reach a WebView with `setJavaScriptEnabled(true)` plus `addJavascriptInterface` (that bridge is scriptable access to Java from JS), and local-file reads when link parameters name files. A quick probe for any WebView-bearing app: `appscheme://webview?url=javascript:...` and `appscheme://webview?url=https://example.com/`.

**Services.** An exported service accepts `startService`/`bindService` from third parties. The recurring finding is a service whose parameters are taken verbatim from the intent — file paths, URLs, commands — letting a foreign app aim the victim app's privileges at attacker data. Prove it with a two-activity PoC harness that binds and invokes.

**Broadcast receivers.** Exported receivers fire on any matching broadcast any app sends. Look for receivers that act on broadcast-supplied extras (fetch a URL, write a file, change a setting) and for apps that trust "system" broadcasts without verifying the sender's permission.

**Content providers.** Providers wrap data (often SQLite) behind `content://` URIs; third-party apps can query them when exported or when granted by `grantUriPermissions`. Two classic primitives: SQL injection at the provider's `selection`/`sortOrder` boundary, and path traversal in FileProvider-backed `openFile()` implementations (`content://.../root/../../../data/data/<pkg>/...` style escapes of the declared `file_paths.xml` roots). Grep the decompiled code for `content://` string literals to find every provider the app itself trusts.

## Getting on the device (device lane)

With a handset (owned test device or engagement-provided), the shell is the workbench: `adb shell`, `pm list packages`/`pm path`, `push`/`pull`/`install`, and `adb shell am`/`pm` to fire the intents built during static analysis. Device identity and settings live under `settings get secure android_id` and the settings provider DB. When the app is debuggable, `run-as com.vendor.app` lands a shell inside the app's UID and turns its private tree into readable loot:

```bash
adb exec-out run-as com.vendor.app cat databases/appname.db > appname.db
```

Private-tree places worth listing after exercising the app: `databases/`, `shared_prefs/` (XML, routinely plaintext tokens), `files/`, `cache/`, and `lib/`. External copies live under `/storage/emulated/0/Android/data/<package>/` and `.../obb/` — readable without root on most configurations and a common leak of "we encrypt before storing… sometimes" data. Backup artifacts (`.ab` files) peel open with a gzip-header prefix plus tar; they contain whatever `allowBackup` let the backup agent carry. For the daemon itself as a *network* target — exposed `adb` on `5555/tcp`, auth model, and history of exposed instances — that is its own unit: [[adbd]].

## Traffic interception

The recurring obstacle: apps targeting Android 7.0+ do not trust user-installed CAs by default, so the Burp/PortSwigger CA pushed as a "user certificate" is ignored by exactly the apps you care about. The matrix:

1. **User CA + NSC patch (no root, artifact in hand).** Add a `network_security_config.xml` `<debug-overrides>` block trusting user certificates, repoint the manifest at it, rebuild, re-sign. Slower, but works on clean devices and inside app-store scope.
2. **System CA push (rooted device).** Convert the proxy's DER cert to the OpenSSL old-style subject-hash name (`<hash>.0`), drop it into `/system/etc/security/cacerts/`, chmod 644, reboot.
3. **Runtime unpinning (pinning on top of trust).** objection's `android sslpinning disable`, or a Frida unpinning script — needed when the app pins its own leaf/intermediate regardless of store trust.

Note which of pinning-vs-trust you are fighting: a clean failure to connect is a trust problem (fix 1/2); a connect-then-reset on one host is pinning (fix 3).

## Instrumentation: Frida, objection, and the arms race

On a **rooted** device: push the matching-architecture `frida-server` binary to `/data/local/tmp/`, chmod 755, run it, and verify with `frida-ps -U`; scripts then attach with `frida -U -f com.vendor.app -l hook.js`. On a **clean** device the order inverts: patch the APK to embed a Frida gadget (objection's patchapk flow or a LIEF-based injector), install the patched build, and connect against the gadget — `objection --gadget com.vendor.app explore` then gives the exploration CLI (`android sslpinning disable`, keystore and filesystem inspection, hooking).

Expect the target to fight back, in increasing order of seriousness: root-detection binaries checking for `su`, Magisk, and superuser APKs (countered by Magisk's own hide mechanisms plus modules like Shamiko); SafetyNet's successor — the **Play Integrity API** — attesting device integrity server-side, where a bypass means spoofing or harvesting valid attestations rather than hiding a binary; emulator detection rejecting your testbed outright; and Frida detection (named pipes/ports, thread maps, `gum` artifacts) that pushes you toward renamed/custom gadget builds. Budget real engagement time here: finance-class apps routinely spend more engineering on anti-analysis than on their actual features.

## Insecure storage and weak-crypto findings

The classes that static review keeps handing back, ranked by how often they turn into report lines:

- **Credentials and keys compiled in.** Auth material, PINs, database-encryption keys, API keys, and server addresses hardcoded — including the lazy variants: base64 "encryption", XOR with a key in the same binary, or a keystore that stores the key *next to* the lock. String-reference tracing finds them: the resource label in `getString(R.string.label)` resolves through `strings.xml`, and the value is usually one grep away.
- **Plaintext private storage.** Tokens in `shared_prefs` XML, session state in unencrypted SQLite under `databases/`, cached responses under `cache/`. The Android Keystore exists precisely so keys live in hardware — code that fetches a key, then writes it to SharedPreferences for convenience, has undone the design.
- **External-storage leakage.** Anything written under `/storage/emulated/0/` is outside the sandbox and, on typical configurations, readable by any app holding broad storage permission — including the screenshots, exports, and "temp" dumps that later outlive the session.
- **Weak primitives.** MD5 for anything security-relevant (collisions are table stakes), and predictable randomness — `java.util.Random` seeded predictably — anywhere a token, nonce, or "random" filename is generated. Both are grep-level findings in decompiled code.
- **Verbose logging.** `logcat` during a login and every interesting action often prints tokens, request bodies, and errors that production builds were supposed to strip. pidcat-style filtered log viewers make this a five-minute check worth doing before deeper work.

## The mobile API behind the app

The app is a client; the engagement usually includes its API. Set up the intercept (CA + pinning bypass above), then test the API as an API: object-level authorization (the mobile equivalent of IDOR — replay the app's own requests with other object IDs and watch authorization decisions), authentication design (API-key-only schemes with no user binding, JWT handling including `alg:none` and weak secrets, refresh-token rotation that never invalidates), session hygiene (logout and timeout actually revoking server-side), rate limiting on the auth and OTP endpoints, and mass assignment where the client's JSON fields map straight onto server models. The app binary is also an API oracle: endpoints, required parameters, and feature flags discovered in decompiled code or the JS bundle frequently reveal attack surface the UI never shows — including admin and test endpoints that the released client still knows how to call.

## Framework apps: Flutter and React Native

Cross-platform frameworks relocate the interesting code out of classes.dex, and the workflow has to follow.

**Flutter.** Identifiable by `lib/<abi>/libflutter.so` (engine) and `libapp.so` (AOT-compiled Dart — the actual app). Standard Java decompilation sees almost nothing. Use reFlutter to repack an instrumented APK (its patched engine enables traffic interception and dumps interesting runtime state), blutter or Doldrums to parse the Dart snapshot back toward readable code, and for pinning know that Flutter bundles BoringSSL and validates certificates itself — ignoring the Android CA stores — so unpinning means hooking the engine's session-verification function, not the system trust path. Framework-native storage (`shared_preferences`, sqflite DBs under the app's private tree) is plaintext and loots like any other database.

**React Native.** All logic ships as JavaScript in `assets/index.android.bundle` — readable directly (beautify with js-beautify, then grep for keys and endpoints) unless the Hermes engine is enabled, in which case the bundle is Hermes *bytecode*: check with `file`, then disassemble with hermes-dec or hbctool. AsyncStorage persists to plaintext SQLite under the app's data directory; native-module bridges (`evaluateJavascript` and custom RN modules) are the injection seam between web bugs and native capability. Deep-link handling (`Linking` listeners) is tested exactly like the native case.

## Recent-Android posture notes

Working assumptions for current devices, kept methodology-shaped because exact API gates move per release: the platform refuses to install apps built against long-dead target SDKs (the floor has risen through the teens — expect legacy-dropper tricks that rely on ancient targetSdk to fail on modern handsets); runtime-registered broadcast receivers must now declare their export behavior (`RECEIVER_EXPORTED`/`RECEIVER_NOT_EXPORTED`), which closes some "any app broadcasts, my receiver obeys" bugs on updated targets; photo/media access is granular (selected-photo grants, visual-media permission groups) so "has gallery access" no longer implies "reads everything"; and background activity launches plus cleartext traffic are increasingly restricted, which shapes what coerced apps can be made to do from the background. Re-check the current version's behavior-change page before relying on any specific gate.

## Mapping to OWASP MASTG

When the report needs standard categories: MASVS-STORAGE covers the filesystem/backup/clipboard loot passes; MASVS-CRYPTO the key handling and algorithm choices; MASVS-AUTH token handling and session invalidation; MASVS-NETWORK TLS posture, pinning, and the network security config; MASVS-PLATFORM the exported components, WebViews, and deep links documented above; MASVS-CODE code-quality and reverse-resistance findings; MASVS-RESILIENCE the anti-tampering/anti-analysis arms race. The categories are a reporting language, not a different method — every row maps onto a phase already in this page.

## Where to go next

- [[mobile-ios]] — the same discipline against the Apple artifact
- [[adbd]] — the debug daemon as a network-exposed service
- [[mobile]] — the area router (lanes and dispatch)
