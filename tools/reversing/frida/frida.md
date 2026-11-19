---
name: "frida"
categories:
  - reversing
homepage: "https://frida.re/"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: c
license: see repository
meta-checked: "2026-11-13"
github-repo: "frida/frida"
github-match: "exact"
github-stars: 22778
github-pushed: "2026-11-13"
github-issues: 1971
gh-checked: "2026-11-13"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
last-refresh: "2026-11-19"
---

# frida

> Dynamic instrumentation toolkit core: injects a JavaScript runtime into running processes and ships the device-side agents (frida-server, frida-gadget) every Frida front-end talks to.

## What it's for

Frida is a dynamic-instrumentation toolkit for developers, reverse-engineers
and security researchers: it injects a JavaScript runtime (QuickJS or V8)
into a target process, where scripts can hook native and Java/ObjC functions,
read and write memory, and intercept crypto and network APIs. This note
covers the CORE project — the injectable engine plus the artifacts that
carry it to a target: `frida-server` (the agent you run on a rooted Android
or jailbroken iOS device), `frida-gadget` (a shared library you embed in an
app when injection is not possible), and the `frida` REPL's device-attach
surface. The same engine is consumed through three sibling layers documented
in their own notes: [[python-frida]] (the importable Python bindings that own
the process/device/script lifecycle from Python code) and
[[python-frida-tools]] (the pip CLI suite — `frida`, `frida-ps`,
`frida-trace`, `frida-pull` and friends). Tools like [[frida-ios-dump]] and
[[frida-extract]] are built on top of these layers. The on-device
frida-server build should match the version of the host-side tools.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Deploy frida-server on a rooted Android device

Put the injection agent on the handset so host tools can reach it.

```
adb shell getprop ro.product.cpu.abilist
unxz frida-server.xz
adb push frida-server /data/local/tmp/
adb shell "chmod 755 /data/local/tmp/frida-server"
adb shell "/data/local/tmp/frida-server &"
```

Outcome: the matching-architecture `frida-server` binary (downloaded from
the Frida releases page) runs as root on the device and exposes the
instrumentation channel over USB/WiFi; `frida-ps -U` from the host lists
device processes when the channel is up. On production builds prefix the
shell steps with `su -c`, and rename or relocate the binary if an app
detects the default path.

### Attach the REPL to a process through a remote frida-server

Instrument a process on a host whose server is reachable over the network.

```
frida -H 198.51.100.10 -p 1234
```

Outcome: the `frida` REPL connects to `frida-server` on the remote host
(instead of `-U` USB) and attaches to pid 1234 there; an interactive
JavaScript prompt opens against that process, and hooks evaluate live.
`-U` in any frida-tools command swaps the same flow to a USB device.

### Embed frida-gadget in a non-rooted app

Make any app instrumentable without root by loading the gadget library.

```
lib/arm64-v8a/libgadget.so
lib/arm64-v8a/libgadget.config.so
# add both files to the APK, repack and re-sign it, install, then attach:
frida -U Gadget
```

Outcome: the app loads the gadget at startup; it starts the default
Listen interaction, appears on a virtual USB device as process `Gadget`, and
attaches normally (`frida -U Gadget`). The optional config file is named
like the library with `.config` appended — on Android it must itself be
prefixed `lib` and suffixed `.so` (e.g. `libgadget.config.so`) or the
package manager will not copy it; renaming the library hides the "Frida"
string from anti-instrumentation checks.

### Load the gadget into a desktop process with LD_PRELOAD

Instrument a Linux binary that cannot be spawned or injected.

```
LD_PRELOAD=./frida-gadget.so ./target
```

Outcome: the dynamic linker runs the gadget's constructor before `main`,
the process blocks in Listen mode and shows up as `Gadget`, ready for an
attach from the REPL. `DYLD_INSERT_LIBRARIES` plays the same role on macOS;
the interaction (Listen, Connect-to-portal, script, script-directory) is
selected in the `.config` JSON file beside the library.

---

## Bug bounty workflow with Frida

Frida is not a scanner — it is the instrument you reach for once a mobile (or
thick-client desktop) target is **in scope** and you need to see what the
binary does that the wire does not show you: pinned TLS you cannot proxy,
tokens built client-side, secrets in memory, and client-side security gates
(root/jailbreak/Frida detection, feature flags, licence checks) that are
trivial to assert but hard to see without hooking. For a mobile program the
unit of scope is almost always a **package name / bundle identifier**
(`com.example.app`), sometimes a specific build, and the backend API behind
it. Written permission, the program's rules of engagement, and your *own*
test device and *own* account are the frame for everything below. The engine
versions this note assumes: Frida core **17.26.3** and frida-tools
**14.13.1** (both November 2026) — keep `frida-server` on the device matched
to the host `frida` version or the attach handshake fails.

The discipline that keeps a Frida engagement clean: **instrument only the
in-scope binary, on hardware you control, against accounts you own.** Frida
attaches to a *process*, so it is easy to drift — into another app, into a
system service, or into reading another user's live session. Don't. A hook
that dumps your own session's token is evidence; a hook that harvests other
users' data is an incident.

### Phase 0 — Lab setup and attach (program-safe)

Goal: a reproducible instrumentation channel to the in-scope app, nothing
else touched.

- **Rooted/jailbroken test device** → push the matching-arch `frida-server`
  (the "Deploy frida-server" case above) and confirm the channel with
  `frida-ps -Uai` (USB, applications + installed). This is the fastest path
  and the one most programs' testers use on a *dedicated* device.
- **Non-rooted device** → let [[objection]] repackage the APK with
  `frida-gadget` embedded (`objection patchapk --source app-release.apk`),
  install the patched build, and attach to `Gadget`. You may only patch a
  copy of the APK you are authorized to test; repackaging a store binary you
  do not have rights to is out of bounds.
- Record the baseline: `frida-ps -Uai` output, the app version, and the
  `frida --version` / `frida-server` version pair. Version mismatch is the
  single most common "it won't attach" cause.

### Phase 1 — Map the runtime surface

Goal: turn the running app into a list of hookable classes and methods, so
Phase 2's hooks are aimed, not sprayed.

- **Android (Java/Kotlin):** `Java.perform()` then
  `Java.enumerateLoadedClasses()`, or objection's
  `android hooking search classes <term>` and
  `android hooking list class_methods <class>`. Hunt the security-relevant
  surfaces: `javax.net.ssl.*` / OkHttp `CertificatePinner` (TLS),
  `javax.crypto.Cipher` and `Mac` (crypto), `SharedPreferences` and
  `SQLiteDatabase` (storage), `KeyStore` (key material), plus any
  app-specific `TokenManager` / `AuthInterceptor` / `RootBeer` classes.
- **iOS/macOS (ObjC/Swift):** `ObjC.available` then `ObjC.classes`, or
  objection's `ios hooking search classes`. Key surfaces: `NSURLSession`,
  `SecTrustEvaluate*`, `NSUserDefaults`, the Keychain (`SecItem*`).
- Native code (`.so`/dylib) surfaces come from `Module.enumerateExports()`
  and `frida-discover` (which finds internal, unexported functions you can
  then trace by offset).

### Phase 2 — API discovery with frida-trace

Goal: watch which of the Phase-1 candidates actually fire during a login,
a purchase, a token refresh — without writing a hook yet. `frida-trace`
auto-generates an editable JS handler per matched function, so it doubles as
a discovery tool and a hook scaffold:

```
frida-trace -U -f com.example.app -j 'javax.crypto.Cipher!*'
frida-trace -U -f com.example.app -i 'libssl.so!SSL_read' -i 'libssl.so!SSL_write'
```

Drive the app by hand; the methods that log are the ones on the hot path.
Promote the interesting handler file to a standalone script for Phase 4.

### Phase 3 — Defeat pinning, open the web surface

Goal: get the app's HTTPS traffic into Burp/mitmproxy. This is the pivotal
phase: most mobile-program payouts are server-side API bugs (BOLA, broken
auth, injection — see [[fastapi]] and the API methodology) that you can only
*reach* once certificate pinning is off and you can replay/modify requests.
objection's one-liner hooks the common pinning implementations
(`TrustManager`, OkHttp `CertificatePinner`, `HttpsURLConnection`, several
native libs); the frida-codeshare scripts are the fallback when a bespoke
pinning layer slips past it (Phase 3 recipes below). Pinning bypass is a
*means* here, not the finding — though a weak/absent pinning that an attacker
on the network could exploit is itself reportable on some programs.

### Phase 4 — Targeted hooking for logic findings

Goal: the actual bugs that need instrumentation. With traffic now visible,
Frida earns its place on the parts the proxy can't reach:

- **Secrets in memory / at the sink:** hook `Cipher.doFinal`, `Mac.doFinal`,
  or a native crypto export and log keys, IVs, and plaintext — hardcoded
  keys and predictable IVs are common Medium/High findings.
- **Client-side security gates:** root/jailbreak detection, Frida/debugger
  detection, and feature/entitlement flags are *client-side assertions*.
  Flipping a `boolean` return (`android hooking set return_value ... true`,
  or a `.implementation` that returns `true`) proves the gate is
  client-trusted. If flipping it unlocks server-honored privilege, that is a
  real finding; if the server re-checks, you've proven the control is
  cosmetic but low-impact — report accordingly.
- **Token handling:** hook where the Authorization header is assembled to see
  signing secrets, nonce reuse, or tokens minted with client-controlled
  claims.

### Phase 5 — Validate and capture

Goal: turn a live hook into a report a triager can reproduce. The evidence
unit for a Frida finding is **the script plus the console transcript** (plus
the intercepted request/response pair when the bug is server-side). A claim
with no runnable script and no transcript is hard to triage — see Reporting
evidence below.

## Where the money is with Frida

Frida rarely *is* the vulnerability; it is what makes a class of mobile/thick
-client findings observable and reproducible. Priorities for an authorized
mobile program:

| Priority | Finding class | Frida feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Server-side API bugs unlocked by unpinning (BOLA, broken auth, injection) | SSL-pinning bypass (objection / codeshare) → proxy → replay | High–Critical | Medium — the *bug* dupes, not the method |
| 2 | Hardcoded/weak crypto keys & IVs | `Interceptor.attach` on `Cipher.doFinal`/`CCCrypt` logging key material | Medium–High | Medium |
| 3 | Auth tokens: client-minted claims, signing-secret leak, nonce reuse | hook header-assembly / JWT-signing method | High | Low–Medium |
| 4 | Sensitive data in local storage | hook `SharedPreferences`/`NSUserDefaults`/SQLite writes; `android keystore list` | Medium | High (often known) |
| 5 | Client-side security gate trusted by server (licence, entitlement, limit) | `.implementation` return-flip; `set return_value` | High if server honors it | Low |
| 6 | Weak/bypassable RASP: root/jailbreak/Frida detection | detection bypass demonstrates the control is client-only | Low–Medium | High |
| 7 | Secrets in memory (API keys, PII) at runtime | `Memory.scan` / heap search for known shapes | Medium | Medium |
| 8 | Deep-link / IPC handler abuse | hook intent/URL handlers to reach unauthenticated internal flows | Medium–High | Medium |

Two realities set expectations. First, **the unpin itself is almost never a
payout** — it is table stakes that opens the server-side surface where the
money is; frame reports around the server bug, citing Frida only as the
reproduction tool. Second, **RASP bypasses dupe heavily**: "I bypassed your
root detection" is a known-issue magnet unless it chains to real impact, so
lead with what the bypass *unlocked*.

## Command recipes

Annotated command → expected output. Low-risk by construction (Frida is not a
network flooder), but two things *can* harm a target: hooking a
high-frequency function crashes or wedges the app, and `Memory.scan` over a
huge range stalls it. Both are called out below.

### Attach and spawn

```
frida-ps -Uai                      # USB: installed + running apps — confirm scope + attach
frida -U -f com.example.app        # spawn the in-scope app, REPL attached, paused at entry
frida -U -n com.example.app        # attach to the already-running app by name
frida -U -p 1234                   # attach by pid (from frida-ps)
frida -H 198.51.100.10:27042 -n Gadget   # attach over the network to a remote frida-server/gadget
```

Spawning with `-f` attaches *before* `main`, which is the only way to hook
early-startup code (pinning setup, root checks) before it runs; the REPL
resumes the process once your script is loaded (older guides pass an explicit
`--no-pause` — check `frida --help` on your build, as the flag's default
behavior has changed across versions). Use `-n`/`-p` when the check you care
about happens later and the app is already up.

### Load a script, keep it attached

```
frida -U -f com.example.app -l unpin.js        # load unpin.js on spawn, hold the session open
frida -U -n com.example.app -l hook.js -o run.log   # tee the console transcript to a file (evidence)
```

Inside the REPL, `%load <file>` and `%reload` re-run an edited script without
detaching — iterate on a hook while the app stays live. `-o` writing the
transcript to a file is the cheapest way to capture Phase-5 evidence.
`--runtime=v8` swaps QuickJS for V8 when a script needs full ES support.

### SSL-pinning bypass (the Phase-3 enabler)

```
# objection: patch common pinning implementations in one command
objection -g com.example.app explore           # newer builds: objection -n Gadget start
# then, at the objection prompt:
android sslpinning disable                      # Android; iOS: ios sslpinning disable
```

```
# one-shot without entering the shell:
objection -g com.example.app explore --startup-command 'android sslpinning disable'
```

```
# frida-codeshare fallback when a bespoke pinning layer survives objection:
frida -U -f com.example.app \
  --codeshare pcipolloni/universal-android-ssl-pinning-bypass-with-frida
```

Expected: after the hook installs, the app's HTTPS requests appear in your
proxy (Burp/mitmproxy configured as the device's CA/proxy). If objection's
generic hooks miss, the codeshare script or HTTPToolkit's
`frida-interception-and-unpinning` script set covers more implementations;
when both miss, Phase 1 told you which pinning class to hook by hand.

### Hook a Java method (Android)

```javascript
// unpin-or-flip.js — replace a method implementation; preserve the signature with overload()
Java.perform(() => {
  const Pin = Java.use('okhttp3.CertificatePinner');
  // check(String, List) — neutralize the pin check
  Pin.check.overload('java.lang.String', 'java.util.List').implementation = function (host, peerCerts) {
    console.log('[pin] bypassed for ' + host);
    return;                       // original threw on mismatch; returning = accept
  };

  // flip a client-side security gate and log every call
  const RootBeer = Java.use('com.scottyab.rootbeer.RootBeer');
  RootBeer.isRooted.implementation = function () {
    console.log('[root] isRooted() -> forced false');
    return false;
  };
});
```

Expected console: a line per invocation, and the app proceeds as if unrooted
/ unpinned. `.overload(types...)` is mandatory when a method name has
multiple signatures; omit it only for uniquely-named methods. Hook a
constructor with `.$init` (Frida replaces `$init`, not `$new`, because
`$new` = `alloc` + `init`). Call the original from inside a replacement by
invoking it normally (e.g. save `const orig = Cls.method; ... orig.call(this, ...)`).

### Hook a native/ObjC function

```javascript
// log arguments and tamper the return of a native export
const p = Module.getExportByName('libnative.so', 'check_license');
Interceptor.attach(p, {
  onEnter(args) { this.arg0 = args[0]; console.log('check_license(' + args[0] + ')'); },
  onLeave(retval) { console.log(' -> ' + retval); retval.replace(1); }   // force "licensed"
});

// replace a function wholesale with a NativeCallback, calling through to the original
const openPtr = Module.getExportByName(null, 'open');
const open = new NativeFunction(openPtr, 'int', ['pointer', 'int']);
Interceptor.replace(openPtr, new NativeCallback((path, flags) => {
  console.log('open(' + path.readUtf8String() + ')');
  return open(path, flags);
}, 'int', ['pointer', 'int']));
```

For ObjC, hook via the method's `.implementation`:
`Interceptor.attach(ObjC.classes.NSURLSession['- dataTaskWithRequest:completionHandler:'].implementation, { ... })`.
In an ObjC `onEnter`, `args[0]` is the receiver (`self`), `args[1]` is the
selector, and real arguments start at `args[2]`. `retval.replace()`,
`this.context` (registers), `this.returnAddress`, and `this.errno` (UNIX) are
available inside the callbacks. `Interceptor.revert(target)` undoes a
`replace`; `Interceptor.detachAll()` drops all `attach` hooks.

### frida-trace for API discovery

```
# Java: every Cipher method; the /isu modifiers = signature match, case-insensitive, user classes only
frida-trace -U -f com.example.app -j '*!*certificate*/isu'

# native: include/exclude by MODULE!FUNCTION glob; -a adds an unexported function by offset
frida-trace -U -f com.example.app -I 'libssl*' -x 'libc.so!*'
frida-trace -U -p 1234 -a 'libjpeg.so!0x4793c'

# ObjC method; Swift uses -y/-Y
frida-trace -U -n Example -m '-[NSURLRequest initWithURL:]'
```

frida-trace writes an editable handler file per matched function
(`onEnter(log, args, state)` / `onLeave(log, retval, state)`); `-d`/`--decorate`
tags each log line with the module name (disambiguates same-named functions),
`-O file` reads options from a file when the command line gets long, and
`-o file` dumps the trace to disk. `-i/-x` filter native functions, `-I/-X`
whole modules, `-j/-J` Java methods, `-m/-M` ObjC, `-y/-Y` Swift, `-s` debug
symbols.

### Script persistence beyond the REPL

A hook that must survive a REPL disconnect, run headlessly in CI, or fire at
app launch on a non-rooted device belongs in `frida-gadget`, not the REPL.
The gadget's `.config` selects a `script` or `script-directory` interaction,
so the agent loads at process start with no host attached (the "Embed
frida-gadget" and "LD_PRELOAD" cases above). For programmatic control from a
host, drive the same script through the [[python-frida]] bindings and expose
callable functions with `rpc.exports = { dumpkey() { ... } }` — the Python
side then calls `script.exports.dumpkey()`. `frida-trace`'s `-S`/`--init-session`
runs a JS file at session start for shared setup across generated handlers.

## Pitfalls, noise and scope discipline

**Instrument the right process, nothing else.** Frida attaches to a pid; it
is one typo from attaching to a different app or a system service. Confirm the
package/bundle id against the program scope on every attach, and work on a
device and account you own. Reading another user's live session through a
hook — even accidentally — is a data-handling incident, not a finding.

**Pinning bypass ≠ the finding.** Reports that stop at "I disabled SSL
pinning" are routinely closed as informative/known. The bypass is a
reproduction step; the report is the server-side bug it exposed, or (where the
program accepts it) the *absence* of pinning as a MITM risk with the network
preconditions spelled out.

**RASP/Frida-detection bypasses dupe hard.** "I bypassed your root/jailbreak
detection" is among the most-duplicated mobile submissions. It is worth points
only when it *chains* to impact (unlocks a paid feature the server honors,
reaches an internal flow). Lead with the chain. And bypassing an app's own
detection on your authorized test device is legitimate dynamic analysis — it
is not defender-evasion; keep it to in-scope targets and don't dress it up as
a standalone critical.

**High-frequency hooks crash the target.** Attaching to something on a tight
loop (allocators, `memcpy`, per-frame render calls, a logging-chatty method)
can wedge or OOM the app — Frida's own docs note per-call overhead measured in
microseconds, which multiplies fast. Hook the narrowest method that answers
your question, filter in `onEnter` before you `console.log`, and avoid
tracing `*!*` on a hot module. A crashed app on the target's device is noise
you created; it can also look like an availability attack.

**`Memory.scan` over huge ranges stalls the process.** Scope scans to a
specific module's range, use a precise byte pattern (nibble wildcards like
`"?3 37 13 ?7"` are fine), and return `'stop'` from `onMatch` once you have
what you need rather than scanning the whole heap.

**Version drift breaks the session silently.** A `frida-server` that doesn't
match the host `frida` version fails to attach or behaves oddly; re-check the
pair before blaming your script. Likewise, don't instrument the device's
production system services to "help" — stay inside the app process.

**Don't exfiltrate beyond proof.** A hook that logs *your* token or *one*
decrypted record of *your* account proves the bug. Dumping the keystore or
scraping records at volume is over-collection — capture the minimum, redact
secrets in the report, and clean up.

## Reporting evidence

A Frida finding is reproducible only if the triager can re-run it. Capture, at
minimum:

- **The exact script** (`.js`), inline in the report or attached — the hook
  *is* the PoC. Keep it minimal: one method, clear `console.log` lines.
- **The invocation**, verbatim: `frida -U -f com.example.app -l hook.js -o run.log`
  (or the objection command), plus the `frida --version` / `frida-server`
  versions and the device/OS.
- **The console transcript** (`-o run.log` output, or the objection session
  log) showing the hook firing and the before/after values — the key that
  leaked, the gate that flipped, the request that changed.
- **For server-side findings**, the intercepted **request/response pair** from
  the proxy after unpinning (the actual bug lives here; Frida is the enabler).
- **A one-line scope statement**: the in-scope package/bundle id, your test
  account, your device — so the triager sees you stayed in bounds.

Structure the report impact-first ("App ships a hardcoded AES key recoverable
at runtime, decrypting all locally stored PII" beats "Frida hook works"),
then preconditions (rooted test device or patched APK), then the
script+command+transcript as copy-pasteable steps, then remediation. Redact
real secrets and real user data from every artifact; never attach another
user's data.

## References

- Frida — homepage and docs: https://frida.re/
- Frida JavaScript API (Interceptor, NativeCallback, Module, Memory, rpc.exports): https://frida.re/docs/javascript-api/
- Frida bridges (frida-objc-bridge, frida-java-bridge): https://frida.re/docs/bridges/
- Frida Android examples (Java.perform / Java.use / .implementation / .overload / $init): https://frida.re/docs/examples/android/
- Frida iOS examples (ObjC hooking): https://frida.re/docs/examples/ios/
- frida-trace documentation (full flag reference, handler generation, options files): https://frida.re/docs/frida-trace/
- frida CLI documentation (`-l`, `%reload`, `--debug`): https://frida.re/docs/frida-cli/
- frida-ps / frida-ls-devices / frida-kill / frida-discover: https://frida.re/docs/frida-ps/
- frida (PyPI, core 17.26.3): https://pypi.org/project/frida/
- frida-tools (PyPI, 14.13.1): https://pypi.org/project/frida-tools/
- objection — repo and wiki (patchapk, start/explore, sslpinning, hooking, memory, keystore): https://github.com/sensepost/objection
- objection wiki, Using objection (`start`, `--startup-command`, `android hooking`): https://github.com/sensepost/objection/wiki/Using-objection
- objection wiki, Patching Android Applications (non-rooted gadget embedding): https://github.com/sensepost/objection/wiki/Patching-Android-Applications
- SensePost, "Using & improving frida-trace" (2025) — Java-method tracing patterns: https://sensepost.com/blog/2025/using-improving-frida-trace/
- HTTPToolkit frida-interception-and-unpinning (pinning-bypass script set): https://github.com/httptoolkit/frida-interception-and-unpinning

*Research supplement added 2026-11-16, grounded in the official Frida docs (frida.re/docs: javascript-api, frida-trace, frida-cli, examples), the Frida/frida-tools PyPI releases (core 17.26.3, tools 14.13.1), and the objection repo/wiki (model: claude-opus-4-8).*
