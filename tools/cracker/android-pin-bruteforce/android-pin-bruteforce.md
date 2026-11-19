---
name: "android-pin-bruteforce"
categories:
  - cracker
version: "git (2023-10-10)"
homepage: "https://github.com/urbanadventurer/android-pin-bruteforce"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "urbanadventurer/android-pin-bruteforce"
github-stars: 4887
github-pushed: "2023-10-10"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 28
---

# android-pin-bruteforce

> Brute-force an Android phone's lockscreen PIN from a second rooted phone acting as a USB keyboard.

## What it's for

Android-PIN-Bruteforce (urbanadventurer) runs on a rooted Kali NetHunter phone wired to the locked phone over USB OTG. It emulates a USB keyboard (HID gadget) and types candidate PINs directly into the lockscreen, honouring the vendor's forced delays after wrong attempts — so it needs no ADB, USB debugging or root on the locked phone. It unlocks Android 6.0.1 through 10.0 devices depending on how the vendor implemented its lockscreen; per-device config files define the key sequences and timings, and frequency-ordered PIN lists try the most common PINs first. `crack` starts a fresh attack, `resume`/`rewind` continue from a known PIN, and `diag` inspects the HID setup.

## Typical use cases

### Crack with the optimised 4-digit list

```
./android-pin-bruteforce crack
```

Outcome: types each PIN from the frequency-ordered 4-digit list into the lockscreen, waiting out configured retry delays, and reports progress; the device unlocks when the correct PIN is entered.

### Restrict candidates with a mask

```
./android-pin-bruteforce crack --mask "19.."
```

Outcome: tries only PINs matching the extended-regex mask (here the years 1900–1999), cutting the search space when some digits are known.

### Dry-run a mask without touching the phone

```
./android-pin-bruteforce crack --mask "...[45]" --dry-run
```

Outcome: prints the PINs the attack would attempt, in order, without sending any keypresses — a safe way to validate a mask before committing hours of typing.

### Use a per-device lockscreen config

```
./android-pin-bruteforce --config ./config.samsung.s5 crack
```

Outcome: loads that vendor's key-sequence and timing profile instead of the default, so PINs are entered and retried the way that device's lockscreen expects.

### Resume an interrupted run

```
./android-pin-bruteforce resume --from 7300
```

Outcome: continues the attack from the given PIN onward instead of restarting at the top of the list.

*No corpus evidence — derived from upstream documentation.*
