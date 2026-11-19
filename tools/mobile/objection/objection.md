---
name: "objection"
categories:
  - blackarch-mobile
version: "1.12.5"
homepage: "https://pypi.org/project/objection/#files"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-06-13"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sensepost/objection"
github-stars: 9488
github-pushed: "2026-10-27"
github-archived: false
github-issues: 55
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 21
gh-activity-checked: "2026-11-09"
---

# objection

> Runtime mobile exploration toolkit powered by Frida — instrument and poke at iOS and Android apps without jailbreak or root.

## What it's for

objection (Sensepost, by Leon Jacobs) is a runtime exploration toolkit built on Frida. It patches an IPA or APK with the Frida Gadget — which is why it works on non-jailbroken, non-rooted devices — or attaches to an already-instrumented process, then drops you into a REPL for live assessment: inspect and interact with the app's container filesystem, dump keychains, bypass SSL pinning, dump and patch memory, and explore and manipulate objects on the heap. Devices connect over USB or the network, apps can be spawned or attached on the foreground app, and a headless single-command `run` mode plus an `api` server make it scriptable. It is also the tool that the archived [[needle]] project points its users to.

## Typical use cases

### Patch an Android APK with the Frida Gadget

Prepare an unmodified retail app for instrumentation on any device.

```
objection patchapk -s target.apk
```

Outcome: unpacks the APK, injects `frida-gadget.so` for the target architecture, rebuilds and signs the result. Install the patched build on the device — it launches paused, waiting for an objection session to resume it (`objection signapk` re-signs arbitrary APKs with the same key).

### Attach the REPL to a patched app

Start exploring a gadget-patched app on a USB-connected device.

```
objection -n Gadget start
```

Outcome: connects to the paused app and opens the exploration REPL with the device and app in the prompt. From there `env` maps the app's data directories, `ls`/`cd` walk them, `filesystem download` pulls files out, `ios plist cat` and `sqlite connect` / `sqlite execute query` inspect plists and databases in place, and `android hooking list activities` enumerates the app's activities for `android intent launch_activity` to invoke.

### Run a single command without the REPL

Do one objection action from a script or a one-liner.

```
objection -n com.example.app run env
```

Outcome: attaches to the named app or bundle identifier, executes just that command, prints its output and exits — the same commands the REPL accepts, without the interactive session.

### Patch an iOS IPA instead

Bundle the FridaGadget dylib into an IPA for non-jailbroken iOS testing.

```
objection patchipa -s target.ipa
```

Outcome: injects the FridaGadget dylib into the IPA and re-signs it (provisioning profile and signature configurable), producing an installable build that pauses at launch until an objection session attaches.

### Automate actions at session start

Run a bypass or setup command the moment the session opens.

```
objection -n com.example.app start --startup-command 'android sslpinning disable'
```

Outcome: the command executes before the REPL polls the device for information, so SSL pinning is already disabled when the prompt appears. `--startup-script FILENAME` instead imports and runs a Frida JavaScript file at the same point, and `--file-commands FILENAME` runs a newline-separated list of objection commands — the hook for scripted, non-interactive app assessments.

### Patch an APK for traffic interception

Make a retail app proxy-friendly at patch time.

```
objection patchapk -s app.apk --network-security-config --enable-debug --use-aapt2
```

Outcome: `--network-security-config` embeds a `network_security_config.xml` that trusts user-installed CAs, so Burp Suite or mitmproxy can read the app's HTTPS traffic on Android 7 and newer; `--enable-debug` sets `android:debuggable` in the manifest, and `--use-aapt2` rebuilds the repackaged APK with aapt2 for APKs that fail to rebuild with the older aapt.

---

# Bug-bounty field guide

**Audience:** bug hunters and red teamers working an **authorized mobile program** — a published scope that lists the app's package name / bundle identifier, written permission, and rules of engagement. objection is an *instrument*, not a scanner: it gets you behind the app's client-side defenses so you can see and exercise the backend the way the real client does. The single rule that governs everything below: **instrument only apps you are authorized to test, on devices and accounts you own.** Patching the Frida Gadget into someone else's retail build, or attaching to another app on a shared device, is out of scope by definition — the patched artifact is *your* test build of an *in-scope* app, nothing else.

A note on "low volume": objection does not flood the network, so the discipline is different from a web target. The risks here are **data** (don't exfiltrate real user records — test with your own accounts), **state** (`memory write`, `memory replace`, `keychain add/update`, `sqlite` writes, and some hooks mutate the running app — avoid them on anything backed by shared/production data), and **stability** (`memory dump all` is large and can OOM or crash the target — run it once, deliberately). Everything below is single-device, evidence-oriented, and reversible.

### What objection can and cannot do

Knowing the boundary keeps your reports honest and your severity claims defensible:

- **It drives Frida; it is not magic.** objection is a curated set of Frida hooks plus a REPL. Anything its built-in commands don't cover you still reach with your own Frida script (`import`, `--startup-script`), but if a generic command fails, the correct conclusion is "the generic hook missed," not "the defense is absent."
- **It runs with the app's privileges, inside the app's process.** `ios keychain dump` sees the app's own access group; `memory dump all`/`memory search` cover the target process only; the filesystem commands reach the app's sandbox. None of this is device- or system-wide compromise — never frame it as such.
- **Two reach models, different footprints.** The **Gadget-patched** route (non-rooted/non-jailbroken) produces a *modified* build — great for reach, but the artifact differs from the store build (signatures, `debuggable`, anti-tamper). The **`frida-server` attach** route (rooted/jailbroken) leaves the app bytes untouched but needs a privileged device. Say which you used; it changes how a triager reads the finding.
- **Client-side bypasses unlock testing; they are not usually the finding.** Defeating pinning, root/jailbreak detection, or biometric gating lets you *reach* code — the payout is the server-side control that turns out to be missing, or the data that turns out to be exposed, once you're behind the gate.

## Bug bounty workflow with objection

Phases 0–1 stand up instrumentation; Phase 2 opens the encrypted channel so the API becomes testable; Phases 3–4 are where client-side payouts live; Phase 5 validates and cleans up.

### Phase 0 — Authorization and lab setup

- Confirm the exact in-scope identifier: Android `com.example.app`, iOS `com.example.App`. objection targets by name (`-n`) — resolve the right one before you touch anything.
- Pick a route. **Non-rooted / non-jailbroken:** repackage the in-scope app with the Frida Gadget (`patchapk`/`patchipa`). **Rooted Android / jailbroken iOS:** run `frida-server` on the device and attach directly — no repackaging, and you can `--spawn` to catch early-launch code.
- Pin versions. objection is a thin layer over Frida; a Gadget/`frida-server` whose version does not match your host Frida is the most common reason a session never opens. objection `1.12.5` is the catalogued build here.
- Baseline the device: one clean `env` + `frida` capture so you can tell app behaviour from device noise later.

### Phase 1 — Get a session

Non-rooted (repackage, install, attach):

```
objection patchapk -s target.apk --use-aapt2
objection patchipa --source target.ipa --codesign-signature <TEAM_ID_HASH>
# install the patched build, launch it (it pauses at start), then:
objection -n Gadget start
```

Rooted/jailbroken (frida-server already running on device):

```
objection -n com.example.app start            # attach to the running/foreground app
objection -n com.example.app --spawn start    # spawn it paused, to hook startup code
objection -N -h 198.51.100.10 start           # device over the network, not USB
```

`-g`/`--gadget` still appears in old blog posts; it is **deprecated and hidden** — use `-n`/`--name`. `explore` is a **deprecated alias of `start`** (same options); prefer `start`. At the prompt, `help <command>` prints usage (upstream warns it is "not always up to date" — the source command tree is ground truth), Tab autocompletes, and `!<cmd>` shells out to the host.

### Phase 2 — Open the channel (SSL pinning) and pivot to the API

The highest-leverage single action: strip the app's certificate pinning so an intercepting proxy can read HTTPS, which turns the opaque mobile client into a visible API surface.

```
objection -n com.example.app start --startup-command 'android sslpinning disable'
# iOS: --startup-command 'ios sslpinning disable'
```

`android sslpinning disable` / `ios sslpinning disable` install Frida hooks for the common pinning implementations (OkHttp, `TrustManager`/`SSLContext`, `NSURLSession`, AFNetworking, and friends). Add `--quiet` to suppress the per-callback job chatter. Once traffic flows to Burp/mitmproxy, you have pivoted off a tool problem onto an **API problem** — BOLA, broken auth, mass assignment, over-exposure — and those server-side findings are where the real severity is. objection got you to the starting line; the API methodology runs the race.

If pinning does **not** drop, that is information, not failure: the app uses custom or native pinning the generic hooks miss. Fall back to a targeted script (`--startup-script custom_pin.js`, or `import custom_pin.js` at the prompt) rather than reporting "no pinning."

### Phase 3 — Data at rest

Everything the app persisted on the device, read in place:

```
env                                   # all app data directories (files, databases, caches, bundle)
ls / cd / pwd                         # walk them
filesystem download prefs.xml ./      # pull a file out for offline analysis (--folder for a dir)
sqlite connect app.db                 # open a DB in place; inspect tables for tokens/PII
ios plist cat Preferences/creds.plist # read an iOS plist
ios nsuserdefaults get                # dump NSUserDefaults (iOS "SharedPreferences")
ios keychain dump --json              # the app's keychain items (its own access group only)
android keystore list                 # entries in the AndroidKeyStore
ios cookies get --json                # the app's cookie jar
ios nsurlcredentialstorage dump       # stored URL credentials
```

Findings here: auth/session tokens, API keys, passwords, or PII written to world-readable or unencrypted stores; secrets in `SharedPreferences`/`NSUserDefaults`; cleartext databases. Severity tracks what leaks and how reachable it is (backup-extractable, log-exposed, other-app-readable).

### Phase 4 — Runtime logic and memory

Attack client-side decisions and in-memory secrets:

```
android hooking search MyAuth                   # find classes/methods by name
android hooking list class_methods com.example.app.Auth
android hooking watch class_method com.example.app.Auth.verify --dump-args --dump-return --dump-backtrace
android hooking set return_value com.example.app.Security.isRooted false
android root disable                             # bypass root detection (for YOUR testing)
ios jailbreak disable                            # iOS equivalent
ios ui biometrics_bypass                         # defeat LAContext local-auth gating
memory list modules --json modules.json         # loaded modules
memory search "api_key" --string --offsets-only # hunt secrets live in memory
memory dump all app_mem.bin                      # full heap dump of YOUR test app (large; once)
android heap search instances com.example.app.Session
android heap print fields 0x...                  # read a live object's fields
```

The `set return_value` / `disable` / `biometrics_bypass` family is for *reaching code behind a client-side gate during your own assessment*. A bypassable root/jailbreak/biometric check is itself usually **Low/Informational** (defense-in-depth), unless it gates something that should have a server-side control — then the finding is the missing server-side control, demonstrated via the bypass.

### Phase 5 — Validate, minimise, clean up

Reproduce the finding from a clean session, capture the **console transcript** (the commands and their output are the evidence), redact real data, remove the patched build, and note that all testing used your own accounts on your own device.

## Where the money is with objection

objection rarely *is* the vulnerability — it is how you reach, prove, or unlock one. Priority by expected payout:

| Priority | Finding class | objection feature that reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Backend bugs behind pinning (BOLA, broken auth, mass assignment) | `sslpinning disable` → proxy the now-visible API | High–Critical | Low (the API bug is unique) |
| 2 | Secrets/tokens/PII in insecure local storage | `env`, `filesystem`, `sqlite connect`, `ios nsuserdefaults get` | Medium–High | Medium |
| 3 | Hardcoded keys / secrets recoverable at runtime | `memory search --string`, `memory dump all`, `memory list modules` | Medium–High | Medium |
| 4 | Keychain/Keystore material exposure | `ios keychain dump`, `android keystore list` | Medium | Medium |
| 5 | Exported component / intent abuse | `android hooking list activities`, `android intent launch_activity` | Medium–High | Medium |
| 6 | Local-auth / biometric bypass | `ios ui biometrics_bypass`, `hooking set return_value` | Medium | Medium |
| 7 | Sensitive data in clipboard/pasteboard | `android clipboard monitor`, `ios pasteboard monitor` | Low–Medium | Medium |
| 8 | Weak/observable crypto usage | `ios monitor crypto`, `hooking watch --dump-args` | Low–Medium | Medium |
| 9 | Root/jailbreak detection bypass | `android root disable`, `ios jailbreak disable` | Low–Info | High |

The pattern: rows 1–4 pay; rows 6 and 9 are often known-issue / informational on mature programs (check the program's explicitly-out-of-scope list before spending the writeup). Row 1 is the big one — objection's value is converting a pinned, opaque client into a testable API.

## Command recipes

Annotated command → what to expect. All identifiers are placeholders for an in-scope target.

**Repackage an Android app for a non-rooted device**

```
objection patchapk -s target.apk --use-aapt2
# -> unpacks APK, injects frida-gadget.so for the detected ABI, rebuilds, signs with objection's key.
#    --use-aapt2 (-2) rebuilds with aapt2 for apps aapt chokes on.
#    --architecture/-a to force an ABI when adb auto-detect is unavailable; --skip-cleanup/-k keeps the work dir.
```

**Make the repackaged app proxy-friendly at patch time (Android 7+)**

```
objection patchapk -s target.apk --network-security-config --enable-debug --use-aapt2
# -> --network-security-config (-N) embeds a network_security_config.xml trusting user-added CAs,
#    so Burp/mitmproxy can read HTTPS without a system-store CA. (Incompatible with --skip-resources.)
#    --enable-debug (-d) sets android:debuggable=true.
```

**Repackage an iOS app** (note `--codesign-signature` is **required**)

```
objection patchipa --source target.ipa --codesign-signature <TEAM_ID_HASH> --provision-file embedded.mobileprovision
# -> injects FridaGadget.dylib and re-signs. -V/--gadget-version pins the Gadget; -b/--binary-name,
#    -B/--bundle-id override identity. Produces a build that pauses at launch awaiting a session.
```

**Attach and auto-disable pinning in one shot**

```
objection -n com.example.app start --startup-command 'android sslpinning disable' --quiet
# -> pinning hooks are live before the REPL polls the device. --startup-command/-s is repeatable;
#    --file-commands/-c FILE runs newline-separated objection commands; --startup-script/-S FILE runs a Frida JS file.
```

**Headless, one command (scriptable)**

```
objection -n com.example.app run env
# -> attaches, runs the single command, prints output, exits. Any REPL command works here.
objection -n com.example.app run "android sslpinning disable"
```

**Harvest stored secrets**

```
objection -n com.example.app explore    # (deprecated spelling of 'start')
# at the prompt:
env
sqlite connect databases/session.db
filesystem download shared_prefs/auth.xml ./auth.xml
ios keychain dump --json keychain.json
# -> look for access tokens, refresh tokens, passwords, PII written without encryption.
```

**Find a secret in live memory without a full dump**

```
memory list modules --json modules.json
memory search "Bearer " --string --offsets-only
# -> cheaper and safer than `memory dump all`; use the full dump only when a targeted search misses.
```

**Scripted, repeatable assessment (CI-friendly, non-interactive)**

```
printf 'android sslpinning disable\nandroid root disable\nenv\n' > run.txt
objection -n com.example.app start --file-commands run.txt --quiet
# -> deterministic startup for a documented, repeatable PoC. Jobs persist; check and tidy them:
jobs list
jobs kill <job-uuid>
import custom_pin.js "custom pinning bypass"   # load your own Frida script when generic hooks miss
```

**Catch startup code (early instrumentation)**

```
objection -n com.example.app --spawn start --startup-command 'ios sslpinning disable'
# -> --spawn/-s launches the app paused so hooks land before the app's own startup runs
#    (pinning set up in Application.onCreate / didFinishLaunching, launch-time token fetches).
# At the prompt, these manage the spawned/attached process lifecycle:
#   resume              # continue a spawned-paused process once hooks are in place
#   reconnect           # re-attach after a disconnect, same process
#   reconnect_spawn     # re-spawn and re-attach
```

Globally, `--no-pause/-p` resumes a spawned target immediately, `--foremost/-f` attaches to whatever app is in the foreground, and `-S <serial>` selects a specific device when several are connected.

**Drive objection as a headless API (scripted fleets)**

```
objection api                                   # start the API server in headless mode
objection -n com.example.app start --enable-api # or expose the API from a live session (-a)
# -> serves on --api-host/-ah (default 127.0.0.1) : --api-port/-ap (default 8888).
#    Keep it bound to localhost; it is an unauthenticated control channel for your own tooling.
```

Low-volume / safety settings to call out in every writeup: `--quiet` on pinning to keep transcripts legible; prefer `memory search` over `memory dump all`; **never** `memory write`, `memory replace`, `keychain add/update`, or `sqlite` writes against data shared with other users; use throwaway test accounts so no dumped record belongs to a real person.

## Runtime exploration deep-dive: classes, heap, and hooks

Phase 4 in detail — this is objection's distinctive capability, and the tool angle the article exists for. The cycle is **enumerate → hook/observe → inspect live objects**. Every command below is a REPL command (or a `run`/`--startup-command` payload).

**Enumerate the class/method surface.** Start from names and narrow:

```
android hooking list classes                              # all loaded classes (large)
android hooking search login --only-classes --json       # grep classes/methods by substring
android hooking list class_methods com.example.app.Auth  # methods on a class
ios hooking list classes
ios hooking list class_methods LoginViewController --include-parents
ios hooking search Credential
```

`hooking search` is the workhorse: it matches both classes and methods, `--only-classes` trims the noise, `--json` makes the output diffable across builds. `android hooking get current_activity` tells you which screen is live so you can scope the search to code that's actually running.

**Observe behaviour by watching.** A *watch* is a background job that logs every invocation:

```
android hooking watch class_method com.example.app.Auth.verify --dump-args --dump-return --dump-backtrace
ios hooking watch "-[LoginViewController submitLogin:]" --dump-args --dump-return --dump-backtrace
```

`--dump-args` shows what the app passed in (often credentials, tokens, device IDs in cleartext), `--dump-return` shows the decision, `--dump-backtrace` shows who called it. Watches accumulate as jobs — `jobs list` to see them, `jobs kill <job-uuid>` to stop one. `android hooking notify` fires when a class/method is touched without dumping; `android deoptimize` forces the ART interpreter when hooks on optimized code misbehave.

**Change a client-side decision.** When the app gates on its own answer:

```
android hooking set return_value com.example.app.Security.isRooted false
ios hooking set return_value "-[JailbreakDetector isJailbroken]" false
```

Use this to *reach* gated code during your assessment. If flipping one boolean opens a flow that should have been enforced server-side, the missing server-side check is the finding.

**Inspect live objects on the heap.** Beyond static methods — reach instances that already exist in memory, with their populated state:

```
android heap search instances com.example.app.Session       # -> live instance handles (hashcodes)
android heap print fields 0x<handle>                         # read that object's fields (tokens, flags, user id)
android heap print methods 0x<handle> --without-arguments
android heap execute 0x<handle> getAuthToken --return-string # call a method on the live object
ios heap search instances SessionManager
ios heap print ivars 0x<handle> --to-utf8                    # iOS ivars, decoded as UTF-8 where sensible
ios heap execute 0x<handle> currentToken --return-string
```

Heap inspection is where "the secret is only ever in memory" findings come from: a token the app decrypts into a `Session` object but never writes to disk is invisible to the Phase 3 storage sweep and visible here. `android heap evaluate` / `ios heap evaluate --inline` run a snippet against a handle for one-off logic; `hooking generate class`/`simple` emit a Frida stub you can refine and re-`import`.

**Passive monitors** (also background jobs) catch data in motion on the device:

```
android clipboard monitor      # sensitive data copied to the clipboard
ios pasteboard monitor         # iOS equivalent
ios monitor crypto             # observe CommonCrypto usage (keys/IVs/algorithms in use)
```

## iOS vs Android command parity

objection mirrors most capabilities across platforms, but the spellings and coverage differ — using the wrong platform's verb is a common first-session stumble:

| Capability | Android | iOS |
|---|---|---|
| Disable transport pinning | `android sslpinning disable [--quiet]` | `ios sslpinning disable [--quiet]` |
| Defeat integrity gate | `android root disable` / `simulate` | `ios jailbreak disable` / `simulate` |
| Local-auth bypass | (hook the check: `hooking set return_value`) | `ios ui biometrics_bypass` |
| Dump app secret store | `android keystore list` / `detail` / `clear` | `ios keychain dump [--json --smart]` / `dump_raw` |
| Key/value prefs | `filesystem` read of `shared_prefs/*.xml` | `ios nsuserdefaults get` |
| Cookie / credential jars | (filesystem/sqlite) | `ios cookies get --json`, `ios nsurlcredentialstorage dump` |
| Structured config file | `filesystem cat` / `download` | `ios plist cat` |
| Enumerate classes/methods | `android hooking list/search ...` | `ios hooking list/search ...` |
| Watch a method | `android hooking watch class_method ...` | `ios hooking watch ...` |
| Live heap inspection | `android heap search/print/execute` | `ios heap search/print/execute` |
| Clipboard capture | `android clipboard monitor` | `ios pasteboard monitor` |
| Component invocation | `android intent launch_activity` / `launch_service` | (UI: `ios ui dump` / `screenshot`) |
| Screenshot / screen protection | `android ui screenshot` / `FLAG_SECURE` | `ios ui screenshot` / `dump` |

## Pitfalls, noise and scope discipline

- **Instrumenting out-of-scope software.** The Gadget-patched artifact must be the **in-scope app**. Do not attach objection to other apps on the device, do not patch apps you were not authorized to test, and do not test on a device that holds someone else's data. This is the line that gets reports rejected and researchers removed from programs.
- **"No SSL pinning" false claim.** When `sslpinning disable` doesn't open traffic, the app likely uses custom/native pinning the generic hooks don't cover. Report "generic objection bypass did not succeed; pinning present" — not "no pinning." Overclaiming here is a fast way to lose triager trust.
- **Scope of the dumps is the app, not the system.** `ios keychain dump` returns only items in the **app's own access group**; `memory dump all` is **your test app's** process memory. Neither reaches other apps or the system keychain — don't frame them as device-wide compromise.
- **Repackaging changes the app.** A patched/`--enable-debug` build is not bit-identical to the store build; signatures differ and anti-tamper may trip. Note in the report that the finding was demonstrated on a Frida-Gadget build, and (where it matters) confirm the behaviour exists in the unmodified app too.
- **User-CA trust needs the config.** On Android 7+, a user-installed proxy CA is ignored unless the app trusts user CAs — hence `--network-security-config` at patch time (or the runtime pinning bypass). Forgetting this reads as "pinning" when it's just the platform CA store.
- **Version drift.** objection ↔ Frida ↔ Gadget/`frida-server` version mismatches cause silent attach failures and confusing errors; align them first before debugging the app.
- **State mutation and crashes.** `memory write`/`replace`, `set return_value`, and `keychain`/`sqlite` writes alter the running app or its data. A corrupted shared backend or a crashed session during a program is noise at best and a scope violation at worst. Keep mutations to local, reversible, own-account state.
- **Leftover artifacts.** Remove patched builds and kill lingering `jobs`/`frida-server` when done; don't leave an instrumented build installed.

## Reporting evidence

The report is the product; objection's output is already evidence-shaped — capture it verbatim.

1. **Title, impact first.** e.g. "Session token stored in cleartext `SharedPreferences`, recoverable without root via Frida Gadget."
2. **Preconditions.** In-scope app + version/build, device (rooted/jailbroken or Gadget-patched), own test account, objection + Frida versions.
3. **PoC — the console transcript.** The exact objection commands and their output, in order. For repackaging, include the `patchapk`/`patchipa` invocation. For storage/memory, include the `env`/`sqlite`/`filesystem download`/`memory search` lines and the returned data (**redacted**). For pinning pivots, include the `sslpinning disable` line *and* the resulting intercepted request/response pair.
4. **Evidence hygiene.** Redact real tokens/PII; dump only what the proof needs; note that only your own account's data was accessed; delete artifacts after.
5. **Remediation.** Tie to the root cause: encrypt data at rest / use the Keychain/Keystore correctly, don't rely solely on client-side pinning or root detection, keep secrets off the device, enforce the control server-side.

Severity rules of thumb: a backend bug reached *through* objection is scored as that backend bug (often High–Critical); insecure data at rest — Medium–High by sensitivity and reachability; recoverable hardcoded secret — Medium–High by what it unlocks; bypassable root/jailbreak/biometric detection on its own — Low/Informational unless it gates a missing server-side control.

## Coverage closeout — falsifiable claims

Know when you are done, and defend it. Frame coverage as claims you *tried to disprove* with objection; a claim you attacked and could not break is evidence, a checklist you ticked is not. Record the command, the result, and the boundary you did not cross.

| Claim you tried to prove | Rejected (secure) if |
|---|---|
| Sensitive data sits in cleartext in the app sandbox | `env` + `sqlite`/`filesystem`/`ios nsuserdefaults get` sweep finds nothing sensitive unencrypted |
| Secrets/tokens are recoverable from the running process | `memory search --string` and live `heap print fields/ivars` surface no credential material |
| The app's secret store leaks to the app itself in the clear | `ios keychain dump` / `android keystore list` return only protected/opaque items |
| Transport pinning can be stripped generically | `sslpinning disable` fails to open traffic (custom/native pinning holds) — and a targeted script also fails |
| A client-side gate (root/jailbreak/biometric) protects a real asset | bypassing it (`set return_value` / `jailbreak disable` / `biometrics_bypass`) reaches nothing the server doesn't re-check |
| An exported component accepts untrusted invocation | `android intent launch_activity`/`launch_service` on it is rejected or harmless |
| Sensitive data leaks via clipboard/pasteboard/crypto misuse | `clipboard`/`pasteboard`/`ios monitor crypto` observe nothing sensitive |

The claim you can rarely close from the client alone: that a control truly exists server-side. When objection shows a client-side check is bypassable, the honest report says "client-side control is insufficient; confirm the server enforces it" — scored on what the backend actually does, not on the bypass.

## References

- objection — GitHub repository (sensepost/objection): https://github.com/sensepost/objection
- objection wiki (home, Using objection, Working with Jobs, Early Instrumentation, Features): https://github.com/sensepost/objection/wiki
- objection CLI entrypoint (click options / subcommands), source of truth for flags — `objection/console/cli.py`: https://github.com/sensepost/objection/blob/master/objection/console/cli.py
- objection REPL command tree (`COMMANDS`), source of truth for commands — `objection/console/commands.py`: https://github.com/sensepost/objection/blob/master/objection/console/commands.py
- Frida (the instrumentation engine objection drives): https://frida.re
- SensePost — "objection: mobile runtime exploration" (project introduction): https://sensepost.com/blog/2017/objection-mobile-runtime-exploration/
- SensePost — "Tip-toeing past Android 7's Network Security Configuration" (the `--network-security-config` rationale): https://sensepost.com/blog/2018/tip-toeing-past-android-7s-network-security-configuration/

*Research supplement added 2026-11-16, grounded in the objection repository, wiki, and the `cli.py`/`commands.py` sources on the `master` branch (github.com/sensepost/objection), plus Frida project docs (model: claude-opus-4-8).*
