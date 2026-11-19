---
name: "byd-dolphin-hacking"
categories:
  - mobile
version: "git (2026-10-07)"
homepage: "https://github.com/wheregoes/byd-dolphin-hacking"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "wheregoes/byd-dolphin-hacking"
github-stars: 73
github-pushed: "2026-10-07"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 174
gh-activity-checked: "2026-11-09"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 5
---

# byd-dolphin-hacking

> Documented research and on-device probe scripts for the BYD Dolphin infotainment head unit (DiLink 3, Android 10).

## What it's for

A community reverse-engineering project for the BYD Dolphin head unit: documentation, findings and small Java probes, discovered entirely through ADB exploration, APK decompilation and CAN bus probing — no proprietary documentation. The unit ships with an unlocked bootloader and exposes ADB over WiFi on port 5555; much of the vehicle API (75+ BYD packages guarded by `BYDAUTO_*` permissions) is reachable from the shell UID without root — AC control, instrument-cluster reads, CAN frame injection through the cluster debug service, AVAS melodies and test tones, and live 360-camera frame capture. The repository is primarily markdown documentation under `docs/` plus runnable `.java` probes under `scripts/` executed on-device through `app_process`; it is not an installable program with a single CLI.

## Typical use cases

### Connect to the head unit over WiFi ADB

```
adb connect 198.51.100.10:5555
```

Outcome: an ADB shell on the Android 10 head unit; the car's own WiFi typically gives the unit a fixed address reachable from a laptop on the same network.

### Run an on-device probe script

```
adb push scripts/BydAudioQuery.java /data/local/tmp/
adb shell "cd /data/local/tmp && app_process -Djava.class.path=. / BydAudioQuery read 0x1B10003D"
```

Outcome: the probe runs in the shell-UID app_process runtime on the unit and prints the requested CAN signal's value — the launch pattern every `scripts/*.java` probe in the repo follows.

### Inject CAN frames via the cluster debug service

```
adb shell "am start -n com.byd.clusterdebug/.MainActivity"
adb shell "am startservice -n com.byd.clusterdebug/.ClusterDebugService"
adb shell "am broadcast -a com.byd.cluster.spi --es normal 'FF,FF,FF,FF,FF,FF,FF,FF'"
```

Outcome: the debug app acts as a privileged proxy and forwards the frame onto the CAN bus — VCDS-style feature coding with no root and no OBD2 dongle.

### Capture the instrument cluster display

```
adb shell "fission_screencap -d 1 -p /data/local/tmp/cluster.png"
adb pull /data/local/tmp/cluster.png
```

Outcome: a PNG screenshot of the driver display (a separate Qt/QML system) is saved to the current directory.

*No corpus evidence — derived from upstream documentation.*
