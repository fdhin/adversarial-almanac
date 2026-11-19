---
name: "android-udev-rules"
categories:
  - blackarch-mobile
version: "20260922.r3.ga9c2aeb"
homepage: "https://github.com/M0Rf30/android-udev-rules"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-24"
build-system: scripts
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
github-repo: "M0Rf30/android-udev-rules"
github-stars: 1361
github-pushed: "2026-10-12"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
---

# android-udev-rules

> A udev rules file (`51-android.rules`) that maps USB vendor/product IDs of Android devices to `adbusers`-group ownership, so adb and fastboot work without root — a configuration artifact, not a program.

## What it's for

android-udev-rules (M0Rf30's community-maintained pack, derived from Google's "Run Apps on a Hardware Device" guidance plus Arch/GitHub community additions) solves a specific Linux annoyance: out of the box, USB device nodes are root-owned, so `adb devices` shows every attached phone as `no permissions` unless adb runs as root. The pack is a single rules file enumerating hundreds of Android vendor and product IDs and telling udev to chown matching USB nodes to the `adbusers` group with mode 0660. The BlackArch package installs it as `/etc/udev/rules.d/51-android.rules` — there is no command of the tool's own to run; "using" it means udev applying the rules and your user being in the `adbusers` group (group creation and membership are install-time steps, covered in install.md). The rules cover adb-mode devices; fastboot uses the same mechanism. New or exotic devices whose IDs are missing can be added by appending a matching rule line with the vendor ID from `lsusb`.

### Confirm the rules are installed and cover your vendor

Check which device IDs the installed rules know about.

```
grep -c idVendor /etc/udev/rules.d/51-android.rules
```

Outcome: prints the number of USB vendor entries (Samsung, Google, HTC, Xiaomi, ...) the rules cover; a nonzero count confirms the package's file is in place. To check a specific device, find its vendor ID in `lsusb` output (e.g. `04e8`) and grep for it in the rules file — an absent vendor explains a device that still shows `no permissions`.

### Apply the rules without rebooting

Make udev pick up the rules after install or after adding an entry.

```
sudo udevadm control --reload-rules && sudo udevadm trigger
```

Outcome: udev re-reads `/etc/udev/rules.d/`; unplug and replug the Android device (and run `adb kill-server` if an adb server from another context is already running) so the new device node is created with the rules applied. On systemd hosts `sudo systemctl restart systemd-udevd.service` is the equivalent hammer.

### Verify a plugged-in device got the right ownership

Confirm the rules actually took effect on the device node.

```
ls -l /dev/bus/usb/*/
```

Outcome: the node for the attached phone (identify it by the bus/device numbers in `lsusb`) shows `root adbusers` ownership with group read/write — after that, `adb devices` lists the device without a permissions warning and `adb shell` works as your normal user. A node still owned `root root` means the rules did not match that device: replug it, then re-check its vendor ID against the rules.

*No corpus evidence — derived from upstream documentation.*
