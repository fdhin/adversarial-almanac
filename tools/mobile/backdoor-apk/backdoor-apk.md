---
name: "backdoor-apk"
categories:
  - blackarch-mobile
version: "141.2710126"
homepage: "https://github.com/dana-at-cp/backdoor-apk"
packaged-by: [blackarch]
blackarch-builddate: "2018-10-03"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "dana-at-cp/backdoor-apk"
github-stars: 2375
github-pushed: "2022-09-12"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# backdoor-apk

> Wrap an existing Android APK around a Metasploit payload via smali injection, interactively.

## What it's for

backdoor-apk is a Bash wrapper (by Dana James Traversie) around apktool, Metasploit and the Android build/sign tooling that embeds a Metasploit RAT into any Android APK. It decompiles the target app, generates a payload APK with msfvenom, merges the payload's smali classes into the original project under randomized package and class names, obfuscates their const-string values, hooks the app's main application class plus a persistence receiver, then recompiles, signs, verifies and zipaligns the result. The workflow is entirely interactive — menus for payload type, LHOST/LPORT and manifest-permission handling — with no options beyond the APK path. It presumes working knowledge of Metasploit, apktool, smali and the Android SDK, and is intended for authorized security testing and education.

## Typical use cases

### Backdoor an APK

```
./backdoor-apk.sh original.apk
```

Outcome: interactive prompts for the Android payload (meterpreter or shell over reverse_tcp/reverse_http/reverse_https), LHOST (use `ATTACKER_IP`) and LPORT, and whether to keep or merge-and-shuffle the manifest permissions; the trojanized, signed and zipaligned APK lands in `original/dist/` alongside a `backdoor-apk.rc` resource script. On BlackArch the installed launcher is simply `backdoor-apk original.apk`.

### Handle the embedded payload

```
msfconsole -r backdoor-apk.rc
```

Outcome: Metasploit loads the generated handler resource (matching the payload, LHOST and LPORT chosen during generation) and listens; installing and opening the backdoored APK on a compatible Android device yields a session.

*No corpus evidence — derived from upstream documentation.*
