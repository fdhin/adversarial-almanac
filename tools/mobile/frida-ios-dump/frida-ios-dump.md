---
name: "frida-ios-dump"
categories:
  - blackarch-mobile
version: "62.8395374"
homepage: "https://github.com/AloneMonkey/frida-ios-dump"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "AloneMonkey/frida-ios-dump"
github-stars: 3991
github-pushed: "2026-11-02"
github-archived: false
github-issues: 107
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 11
gh-activity-checked: "2026-11-09"
---

# frida-ios-dump

> Dump a decrypted IPA from a jailbroken iOS device through Frida and SSH.

## What it's for

frida-ios-dump runs a Frida agent on a jailbroken device, spawns or attaches to
a target app, and dumps its decrypted binaries and resources, then pulls them
over the device's SSH service and packages a decrypted `.ipa` for static
analysis. Because iOS decrypts app binaries only at runtime, this memory-dump
route is the standard way to obtain a decrypted Payload. It requires
frida-server on the device and matching Frida tooling on the host — see
[[python-frida-tools]] for the host-side CLI suite. SSH reaches the device
through Frida's USB channel by default (default device credentials
`root`/`alpine`); `--remote` drives devices reachable only over the network.

## Typical use cases

### List installed apps

Enumerate what can be dumped.

```
./dump.py -l
```

Outcome: prints each installed app's display name and bundle identifier — the
two forms accepted as the dump target.

### Dump an app over USB

Pull a decrypted IPA from a USB-attached device.

```
./dump.py com.example.app
```

Outcome: spawns the app, dumps the main binary, frameworks and resources in
parallel, and writes `com.example.app.ipa` in the working directory (a display
name such as `'Target App'` works too).

### Use non-default SSH credentials

Point the transfer channel at another account, password or key.

```
./dump.py -u mobile -P 'Password1' com.example.app
```

Outcome: authenticates to device SSH as `mobile` with the given password; `-K`
selects a private key instead, and keys are tried before passwords.

### Select a device or connect remotely

Work with several attached devices, or with one reachable only by network.

```
./dump.py --device-id DEVICE_ID -l
./dump.py --remote 198.51.100.10:27042 -l
```

Outcome: the first form lists apps on the USB device whose ID came from
`frida-ls-devices`; the second drives a device whose frida-server listens at
198.51.100.10:27042, with SSH defaulting to TCP on the same host.

*No corpus evidence — derived from upstream documentation.*
