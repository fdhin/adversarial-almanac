---
name: "jadx"
categories:
  - forensics
  - reverse-engineering
version: "1.5.6-1"
homepage: "https://github.com/skylot/jadx"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-07-18"
build-system: java
meta-checked: "2026-11-09"
github-repo: "skylot/jadx"
github-stars: 51660
github-pushed: "2026-11-02"
github-archived: false
github-issues: 478
github-match: "exact"
gh-checked: "2026-11-07"
worth: 9.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 213
gh-activity-checked: "2026-11-09"
---

# jadx

> Dex-to-Java decompiler: turns Android APK/DEX artifacts into readable Java source and decoded resources, from either a CLI or a GUI.

## What it's for

jadx decompiles Dalvik/ART bytecode (.apk, .dex, .aar, .aab, .xapk, .jar, .class, .smali) back to Java source and decodes Android resources (manifest, XML, strings) into browsable form. It is the standard first-pass static-analysis tool for Android apps: recovering logic, hunting hardcoded secrets, and reading obfuscated code after automatic renaming. Output quality is best-effort — some constructs decompile incorrectly (marked with `// jadx error` comments); `--show-bad-code` forces their inclusion and fallback mode emits linearized instructions instead of restructured code. The CLI (`jadx`) batch-decompiles for automation; the GUI (`jadx-gui`) adds interactive browsing, search, and deobfuscation-mapping editing. It does not execute or emulate the app.

## Typical use cases

### Decompile an APK to Java sources and resources

Batch-recover an app's code for static analysis.

```
jadx -d output app.apk
```

Outcome: writes decompiled Java packages under `output/sources/` and decoded resources (AndroidManifest.xml, res/) under `output/resources/`. Errors on individual classes are logged; the rest of the app still decompiles.

### Deobfuscate while decompiling

Make renamed-minified code readable.

```
jadx --deobf -d output app.apk
```

Outcome: activates deobfuscation — identifiers that are too short, too long, or invalid Java names are systematically renamed to stable `ClassNameN`/`methodnameN` aliases, with the mapping kept in a `.jobf` file next to the input for reuse across runs.

### Save as an importable Gradle project

Get decompilation output into a buildable project.

```
jadx -e -d project app.apk
```

Outcome: exports the app as a Gradle project structure (`--export-gradle`), which an IDE can open for indexed navigation, searching and cross-referencing of the decompiled sources.

### Browse an app interactively

Explore and search code before committing to a full export.

```
jadx-gui app.apk
```

Outcome: opens the GUI with the APK loaded as a project — tree navigation of classes and resources, full-text search, a deobfuscation-mapping editor with autosave, and manual export of selected classes. Preferred for exploratory analysis; the CLI is preferred for automation.

---

## Bug bounty workflow with jadx

jadx is a **static** tool: it reads an app and never runs it. On a mobile bug bounty program your highest-value output from jadx is not a vulnerability by itself — it is a *map*. Decompiled sources hand you the app's endpoints, headers, auth logic, hardcoded material, exported attack surface and client-side checks; you then prove impact against the live backend (paired with [[mitmproxy]]) or on-device (adb / [[frida]]). The code is evidence of where to look; the finding is almost always confirmed somewhere else.

Everything below assumes you are inside an authorized program whose scope **explicitly covers the mobile app** (many programs scope only the web backend and treat the APK as out of scope — read the brief). A decompiled APK is untrusted data: analyze it, never execute it, and keep it in its own directory.

### Phase 0 — Acquire the artifact legitimately and fingerprint

Get the exact build the program is targeting, and record its identity so every later finding is pinned to a version.

- **Preferred source: your own device.** Install the app from the official store on a test device/account you own, then pull it: `adb shell pm path com.example.app` → `adb pull <path> base.apk`. Split APKs (AAB-delivered apps) pull as multiple files (`base.apk`, `split_config.*.apk`); jadx reads `.apk`, `.apks`, `.xapk`, `.apkm` and `.aab` directly, so you can load the whole set.
- **Third-party mirrors** (APKMirror/APKPure) are convenient but you lose provenance — only use them if the program allows, and still verify the `versionCode`/signing cert matches the store build.
- **Record identity before anything else**: `versionName`/`versionCode` from the manifest, the signing certificate, and the jadx version you used. These three go in every report header.

```
jadx --version
jadx -d out/ -j 4 base.apk     # first-pass decompile, see Phase 1
```

Expect `// jadx error` / `// Can't load class` comments scattered through output — that is normal; it does not mean the decompile failed. Note in your working log whether the app looks obfuscated (one- and two-letter class/method names everywhere) — that decides whether you invest in `--deobf` and mapping files (Phase 3).

### Phase 1 — Decompile and map the manifest (attack surface)

The decoded `AndroidManifest.xml` is the single densest recon artifact in an APK. Decompile once, then read the manifest before the code.

```
jadx -d out/ base.apk
# out/resources/AndroidManifest.xml  — decoded manifest
# out/sources/...                     — Java packages
# out/resources/res/, assets/        — strings, configs, raw files
```

Harvest from the manifest, in priority order:

1. **Exported components.** Any `activity`, `service`, `receiver`, or `provider` with `android:exported="true"` (or with an `intent-filter` and no explicit `exported`, which is implicitly exported on older `targetSdk`) is reachable by any other app on the device. These are the classic mobile IDOR/access-control surface — an exported activity that renders account data, a `ContentProvider` that serves files, a `BroadcastReceiver` that performs a privileged action on an attacker-sent intent.
2. **Deep links.** `intent-filter` with `android:scheme`/`android:host` (`<data>`) — these are attacker-reachable entry points, candidates for open redirect, intent injection, and auth-token interception.
3. **`android:debuggable="true"`** — shipping a debuggable build is itself a finding (lets any local app attach a debugger / run as the app).
4. **`android:allowBackup="true"`** (default) — app data extractable via `adb backup` without root on permissive devices.
5. **`networkSecurityConfig`** — read the referenced `res/xml/network_security_config.xml`: cleartext-traffic permits, trust-anchors that add user CA store (defeats pinning), per-domain exceptions.
6. **Custom permissions** with `android:protectionLevel="normal"` guarding sensitive components — a weak gate.

**Deliverable from Phase 1** — a component inventory like the one below. Every exported row with no permission and a data-bearing or state-changing handler goes to the top of your Phase 3 reading queue and your Phase 5 on-device PoC list.

| Component | Type | Exported? | Permission? | Handler reads | Verdict |
|---|---|---|---|---|---|
| `.ui.AccountActivity` | activity | true | none | `account_id` extra | access-control candidate |
| `.TransferReceiver` | receiver | true | none | `url`, `amount` extras | intent-injection candidate |
| `.provider.FileProvider` | provider | true | `normal` | `uri` path segment | traversal / IDOR candidate |
| `.sync.SyncService` | service | false | — | — | not reachable, skip |

### Phase 2 — Secret and endpoint sweep

The second pass is a grep sweep over decompiled sources **and** resources/assets. Client apps routinely ship material that was never meant to be shippable.

- Cloud/service keys: AWS (`AKIA…`), Google API keys (`AIza…`), Firebase config, GCP service-account JSON, Stripe (`sk_live_…`), Twilio, Sentry DSNs, Mapbox, Algolia admin keys.
- `google-services.json` artifacts: project IDs, Firebase DB URLs, GCM/FCM keys — a Firebase DB URL is one unauthenticated `curl …/.json` away from confirming an open database.
- Backend hostnames and `/api/…` paths — including **staging/internal** hosts not reachable from the web app, which can pivot a mobile finding into a web-scope one.
- Hardcoded symmetric keys/IVs used by the app's own crypto (enables offline decryption of stored data).

jadx-gui's full-text search (with "search in code" + "search in resources") is the interactive version; for automation, decompile with the CLI and grep the tree, or let a wrapper like [[apkleaks]] (which drives jadx under the hood with a secret-regex pack) or [[trufflehog]]'s filesystem mode do the first sweep. **jadx is the decompiler; the secret scanners are regex over its output** — understand that split so you can hand-verify every hit (scanners are noisy).

### Phase 3 — Read the logic: auth, crypto, client-side checks

Now read code, not strings. On obfuscated apps, decompile with deobfuscation so names stay stable across sessions and the mapping is reusable:

```
jadx --deobf --deobf-cfg-file app.jobf --deobf-cfg-file-mode read-or-save -d out/ base.apk
```

What to read for, and what each pattern is worth:

- **Authentication / token handling.** Where the app mints, stores, and attaches tokens; refresh logic; whether secrets for signing requests (HMAC of request bodies, API signatures) are hardcoded — if they are, you can forge signed requests the backend trusts.
- **Client-side-only gates.** Feature flags, paywalls, "is premium", role checks evaluated *in the app*. These are bypassable by definition; the finding exists only if the *server* also fails to enforce (test that, don't assume).
- **Weak/improper crypto.** `DES`, `ECB` mode, hardcoded keys/IVs, `MD5`/`SHA1` for integrity, static salts — read the `javax.crypto`/`Cipher.getInstance(...)` call sites.
- **Root / emulator / pinning detection.** Locate it in source now so you know what [[frida]] will need to neutralize for dynamic testing later. jadx finds *where* the check is; it does not bypass it.
- **Insecure storage / logging.** `SharedPreferences`, `getExternalFilesDir`, `Log.d(...)` of tokens/PII, WebView `setJavaScriptEnabled` + `addJavascriptInterface`.

### Phase 4 — Build the live API map (pair with mitmproxy)

Static endpoints from Phase 2 become a tested surface only against the running app. Run the app through [[mitmproxy]] on a device you control and reconcile the two views:

- jadx gives you **every** endpoint compiled into the app, including ones the app only calls in states you cannot easily reach (admin, feature-flagged, error paths). mitmproxy gives you the **live** requests with real headers, tokens, and bodies.
- The gap is the prize: endpoints in the decompiled code that *never appear* in captured traffic are the least-exercised, least-reviewed routes — feed them to your backend testing (BOLA/auth checks) with real headers lifted from the live capture.
- jadx also reveals the **request-signing scheme** (which header, which secret, which fields) so you can replay and mutate signed requests in mitmproxy that would otherwise look tamper-proof.

### Phase 5 — Patch/repack is usually the wrong move

jadx **does not repack**. It is a decompiler; it emits Java/smali/resources, not a signed APK. To modify-and-reinstall you would reach for `apktool` + `apksigner`, and for dynamic bypasses (pinning, root detection) [[frida]] is cleaner than patching bytecode. Two hard rules for bounty work:

- **Never redistribute** a decompiled, modified, or repacked copy of a target app. It is copyright infringement and almost always a program-rules and app-store-ToS violation. Patching for *your own on-device testing* is one thing; publishing or sharing the artifact is out of bounds.
- Prefer runtime instrumentation over repacking when you only need to observe or bypass a client-side check — it leaves the shipped binary untouched and is easier to document.

### Code patterns worth reading for

When you reach Phase 3, these are the decompiled shapes that most often convert to findings. The decompiled Java will not look exactly like this (names/structure vary, especially post-obfuscation), but the call sites are what you search for.

```java
// WEAK — exported component performs a privileged action with no caller check
// (manifest: android:exported="true", no android:permission)
public void onReceive(Context ctx, Intent intent) {
    String target = intent.getStringExtra("url");
    account.transferTo(target, intent.getLongExtra("amount", 0)); // any app can send this
}
// STRONG — guarded by a signature-level permission and/or verifies the caller
```

```java
// WEAK — WebView bridge exposes native methods to any loaded page
webView.getSettings().setJavaScriptEnabled(true);
webView.addJavascriptInterface(new NativeBridge(), "Android"); // + loads attacker-influenced URL
// Reachable JS -> native method calls; combined with a loadUrl() you can influence = RCE-adjacent.
// STRONG — @JavascriptInterface only on vetted methods, fixed trusted origin, no user-controlled loadUrl
```

```java
// WEAK — hardcoded request-signing secret: the backend trusts a signature any client can forge
String sig = HmacSHA256("f1x3d-cl13nt-s3cr3t", method + path + body); // secret shipped in the APK
request.addHeader("X-Sig", sig);
// Lift the secret from jadx, replay/mutate signed requests in mitmproxy that look tamper-proof.
```

```java
// WEAK — exported ContentProvider over a file-backed path, no path sanitisation
public ParcelFileDescriptor openFile(Uri uri, String mode) {
    return ParcelFileDescriptor.open(new File(BASE, uri.getLastPathSegment()), ...); // ../ traversal
}
// content:// query from any app -> read private files the provider never meant to expose.
```

For each, jadx tells you the pattern *exists and where*; the finding needs the on-device or on-the-wire proof below.

### On-device PoC for a mapped component

Once jadx's manifest + code reading identifies a reachable component, prove it with `adb` (these are the *downstream proof*, not jadx). Use a test device/account you own.

```
# Exported activity — launch it directly and pass the extras the code reads
adb shell am start -n com.example.app/.ui.AccountActivity --es account_id 12345
# -> renders without going through login? that's the access-control finding.

# Exported broadcast receiver — fire the intent its onReceive() parses
adb shell am broadcast -a com.example.app.ACTION_TRANSFER --es url https://attacker.example --el amount 100

# Exported ContentProvider — query / read files it serves
adb shell content query --uri content://com.example.app.provider/users
adb shell content read --uri content://com.example.app.provider/../../databases/app.db

# Deep link handler — hand the app an attacker-shaped URI
adb shell am start -a android.intent.action.VIEW -d "exampleapp://open?next=https://attacker.example"
```

Capture the full command and the resulting screen/output/logcat; that pair (jadx code → adb invocation → observed effect) is the complete finding.

---

## Where the money is with jadx

jadx surfaces the *candidate*; the severity is realized by the downstream proof. Dupe risk is high for anything an automated scanner (apkleaks/trufflehog, MobSF) also finds — assume the low-hanging secrets are picked over on mature programs.

| Finding class | What in jadx reaches it | Typical severity | Dupe risk |
|---|---|---|---|
| Live cloud credentials (AWS/GCP/Stripe/Twilio) | string sweep of `sources/` + `resources/` (Phase 2) | High–Critical (if valid & privileged) | **High** — first thing every scanner checks |
| Open Firebase / backend DB | `google-services.json` → DB URL, confirm with unauth request | High–Critical | High |
| Exported activity/provider leaking data or action | manifest `exported="true"` + reading the component's code | Medium–High | Medium |
| Deep-link / intent injection, open redirect | `intent-filter` `<data>` + handler code | Medium–High | Medium |
| Hardcoded request-signing secret → forged signed API calls | crypto/signing call sites in source | High | Low–Medium |
| Hidden/internal/staging endpoints → backend pivot | endpoint strings not seen in live traffic | varies (inherits backend bug) | Low |
| Weak crypto / hardcoded key enabling offline decryption | `Cipher.getInstance` call sites | Medium | Medium |
| `debuggable=true` / `allowBackup=true` / cleartext config | manifest + `network_security_config.xml` | Low–Medium | High |
| Client-side-only paywall/role gate (server also fails) | client gate in source + server test | Low–High (server-dependent) | Medium |
| Verbose logging of tokens/PII | `Log.*` call sites | Low | High |

The pattern that pays best: a **hardcoded secret or hidden endpoint that pivots into the backend**, where the mobile app was merely the delivery mechanism for a server-side bug. A string in an APK is Info until it is proven live.

---

## Command recipes

Annotated commands. jadx is local and offline — there is no rate-limit or DoS risk to the *target* from running it (it never touches the program's infrastructure). The discipline here is about correctness and not drowning in noise.

```
# Full first-pass decompile, bounded threads (default is half your CPU threads)
jadx -d out/ -j 4 base.apk
# -> out/sources/ (Java), out/resources/ (manifest, res/, assets/). Per-class
#    errors are logged and skipped; the rest still decompiles.
```

```
# Resources/manifest only — fast attack-surface triage, skip the slow Java pass
jadx -s -d out/ base.apk          # -s/--no-src: no sources
# -> decoded AndroidManifest.xml and res/ in seconds; read exported components first
```

```
# Deobfuscate with a reusable mapping file so names survive across runs
jadx --deobf --deobf-cfg-file app.jobf --deobf-cfg-file-mode read-or-save \
     --deobf-min 3 --deobf-max 64 -d out/ base.apk
# -> short/invalid identifiers get stable ClassNameN/methodNameN aliases;
#    read-or-save creates app.jobf if absent, reuses it if present.
```

```
# Import a mapping you (or the GUI) produced — Enigma/Tiny/Tiny-v2 format
jadx --mappings-path app.mapping --mappings-mode read -d out/ base.apk
# -> applies human-reviewed names; --mappings-mode read is the default (read-only).
```

```
# Export as a Gradle project for IDE indexing / xref navigation
jadx -e --export-gradle-type auto -d project/ base.apk
# -> open project/ in Android Studio / IntelliJ for Find-Usages across the app.
```

```
# Decompile a single class by fully-qualified name (surgical, fast)
jadx --single-class com.example.app.net.ApiClient -d one/ base.apk
# -> only that class, useful when you already know the target from search.
```

```
# Force inclusion of inconsistent ("bad") code jadx would otherwise drop
jadx --show-bad-code -d out/ base.apk
# -> emits classes that failed restructuring (with // jadx error markers) so you
#    can still read logic the clean pass hid. Noisy; use per-class when possible.
```

```
# Fallback when restructuring mangles something — linearized, closer to bytecode
jadx -m fallback -d out/ base.apk        # -m/--decompilation-mode: auto|restructure|simple|fallback
# -> use 'simple' or 'fallback' to sanity-check a method jadx decompiled oddly.
```

```
# Secret/endpoint sweep over the decompiled tree (jadx + your own grep)
jadx -d out/ base.apk
rg -n -i 'AKIA[0-9A-Z]{16}|AIza[0-9A-Za-z_\-]{35}|sk_live_|-----BEGIN|firebaseio\.com|s3\.amazonaws' out/
# -> hand-verify every hit; these regexes are the same ones apkleaks/trufflehog use.
```

```
# Plugin management and per-plugin tuning (-P<name>=<value>)
jadx plugins --list-all
jadx -Pdex-input.verify-checksum=no -d out/ base.apk     # tolerate patched/partial dex
jadx -Pkotlin-metadata.class-alias=yes -d out/ base.apk  # recover Kotlin names from @Metadata
# -> smali-input.api-level, java-convert.mode (dx|d8|both) are other -P knobs.
```

```
# Batch-decompile a directory of APKs (version diffing, fleet triage)
for apk in apks/*.apk; do
  jadx -s -d "out/$(basename "$apk" .apk)" "$apk"   # -s: manifest/res first-pass
done
# -> one output tree per app; diff out/<v1>/resources/AndroidManifest.xml against
#    out/<v2>/... to spot newly exported components or new endpoints between builds.
```

```
# Decompile a split-APK set (AAB-delivered app) in one project
jadx -d out/ base.apk split_config.en.apk split_config.arm64_v8a.apk
# -> jadx accepts multiple inputs; load base + splits so code and resources that
#    live in different splits resolve against each other.
```

For large apps, a resources-only pass (`-s`) to read the manifest, then a targeted `--single-class` dive, is far faster than fully decompiling then searching — reach for the full `-d` export only when you want IDE-wide cross-referencing.

### Deobfuscation mappings: the GUI→CLI round-trip

On a heavily obfuscated app, the productive loop is: browse in `jadx-gui`, rename classes/methods/fields as you understand them (right-click → Rename, which the GUI autosaves to a mapping), then feed that human-reviewed mapping back into the CLI for a clean scripted export. jadx reads/writes **Enigma, Tiny and Tiny-v2** mapping formats via `--mappings-path`, with `--mappings-mode` controlling behavior (`read` is default/read-only; `read-and-autosave-every-change` and `read-and-autosave-before-closing` persist GUI renames; `ignore` disables).

```
# After naming things in the GUI, re-run the CLI with your reviewed mapping
jadx --mappings-path app.mapping --mappings-mode read -e -d project/ base.apk
# -> deterministic, human-readable names in the exported project; your report's
#    class/method references stay valid because the mapping pins every alias.
```

Keep the mapping (and/or the auto `--deobf-cfg-file` `.jobf`) with your engagement notes — it is what makes a six-month-old finding reproducible against the same build. The auto-`--deobf` aliases (`ClassNameN`) are for *reading*; the mapping file is for *citing*.

### smali when Java lies

jadx best-effort reconstructs Java/Kotlin; when a method decompiles wrongly it flags `// jadx error` and you fall back to lower-fidelity views. The fidelity ladder, cheapest to most faithful: `-m restructure` (default, most readable) → `-m simple` (less rewriting) → `-m fallback` / `--show-bad-code` (linearized, instruction-level, closest to the real bytecode). For a disputed method, read the smali/fallback view before you draw a security conclusion — the restructured Java can misrepresent control flow, and a wrong reading in a report burns credibility. jadx also *accepts* `.smali` and `.dex` as input, so you can round-trip bytecode patched elsewhere back through it to read the result.

---

## Pitfalls, noise and scope discipline

jadx cannot hurt the target (it never contacts it), so the discipline is about **report quality, legal scope, and not wasting a triager's time**.

- **Scope: is the APK actually in scope?** Many programs list only web assets; the mobile app, its decompiled code, and the backend it talks to may each be scoped differently. Decompiling an out-of-scope app, or reporting a client-side-only "finding" with no server impact, gets reports closed as N/A.
- **A hardcoded string is not a vulnerability until proven live.** Reporting `AKIA…` without demonstrating it authenticates to a privileged resource is the #1 reason secret reports get closed as informational. Validate against the service (with the owner's blessing / within rules) before you claim impact — and never dump data beyond the minimum proof.
- **Scanner dupes.** apkleaks, trufflehog and MobSF scrape the same decompiled output. On a mature program the obvious secrets are already reported; your edge is manual reading (signing schemes, logic flaws, hidden endpoints) that regex misses.
- **Client-side gates ≠ server enforcement.** A bypassable paywall/role check in the app is only a finding if the *server* also fails to enforce it. Test the backend; do not report "I patched the premium flag" as a vulnerability on its own.
- **`// jadx error` / `// Can't load class` are decompiler limitations, not app bugs.** Do not report decompilation artifacts. When logic looks wrong, cross-check with `-m fallback`/`--show-bad-code` or read the smali before drawing conclusions.
- **Never redistribute the app or a repacked build.** Decompile for analysis; do not publish, share, or host the APK, its sources, or a modified copy. This is both a legal and a program-rules line.
- **Obfuscated output changes between runs.** Without a saved `--deobf-cfg-file`/mapping, auto-generated aliases can differ run to run — pin a mapping file so your report's class/method references stay valid.
- **Untrusted-archive safety.** jadx ships zip-bomb and XML-entity protections (`JADX_ZIP_*`, `JADX_DISABLE_XML_SECURITY` env vars). Do **not** disable them (`JADX_DISABLE_ALL_SECURITY_FLAGS`) on an APK you did not build — a malicious archive can exhaust memory/disk on your analysis box.

---

## Reporting evidence

A mobile static finding is only as good as the chain from "code says X" to "the live system does X". Capture:

1. **Artifact identity.** App package name, `versionName`+`versionCode` (from the decoded manifest), signing cert fingerprint, and the store/source you pulled it from — so the triager can reproduce on the exact build.
2. **Tool provenance.** jadx version (`jadx --version`) and the exact command you ran (including `--deobf`/mapping flags), so your decompiled line references are reproducible.
3. **The decompiled snippet, with location.** The fully-qualified class + method and the relevant lines — e.g. `com.example.app.net.ApiClient.sign()` — pasted as text (not only a screenshot), plus the surrounding few lines for context. If deobfuscated, note the original obfuscated name too.
4. **The manifest excerpt** for component/permission findings — the exact `<activity …>`/`<provider …>` block showing `exported`, `permission`, and `intent-filter`.
5. **The live proof.** The request/response pair (via [[mitmproxy]]) for an endpoint/auth/signing finding; the `adb shell am start -n com.example.app/.TargetActivity …` (or `am broadcast`/`content query`) invocation and its result for an exported-component finding; the validated-but-redacted output for a live-credential finding (prove access, do not exfiltrate).
6. **Impact framing.** Tie the client-side observation to a server-side or on-device consequence. "Hardcoded key in `ApiClient.sign()` lets any client forge the `X-Sig` header the backend trusts → authenticated API access without a session" beats "found a hardcoded key".

Redact secrets in the public report to the minimum that proves the point (first/last few chars), clean up any test data you created, and never attach the decompiled sources or the APK itself.

---

## References

- jadx README (CLI usage, all options, plugins, env vars) — https://github.com/skylot/jadx/blob/master/README.md
- jadx repository and wiki — https://github.com/skylot/jadx
- jadx releases (1.5.x notes: external plugins, Kotlin scripting, `.apks`/APKM support) — https://github.com/skylot/jadx/releases
- OWASP Mobile Application Security Testing Guide (MASTG) — https://mas.owasp.org/MASTG/
- YesWeHack, Android recon for bug bounty (APK extraction → attack-surface mapping) — https://www.yeswehack.com/learn-bug-bounty/android-recon-bug-bounty-guide
- apkleaks (jadx-driven secret/endpoint scanner) — https://github.com/dwisiswant0/apkleaks
- Truffle Security, cracking open APK files at scale (jadx + trufflehog pipeline) — https://trufflesecurity.com/blog/cracking-open-apk-files-at-scale
- Android `android:exported` / component security — https://developer.android.com/guide/topics/manifest/activity-element#exported

*Research supplement added 2026-11-15, grounded in the jadx README/releases on github.com/skylot/jadx and the OWASP MASTG / YesWeHack Android recon sources above (model: claude-opus-4-8).*
