---
name: "appmon"
categories:
  - blackarch-mobile
version: "0.5.r36.gf753c4d"
homepage: "https://github.com/dpnishant/appmon"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "dpnishant/appmon"
github-stars: 1633
github-pushed: "2023-05-01"
github-archived: true
github-issues: 38
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# appmon

> Frida-based framework to monitor and tamper with system/native API calls of macOS, iOS and Android apps.

## What it's for

AppMon drives Frida against a target app from a Python CLI. The sniffer attaches to a running process (or spawns it), injects your Frida agent script(s), and records the API calls the app makes — arguments and return values — into dump files and a report database under `./app_dumps/`; the same interception lets an intruder-mode script modify call data to change the app's behaviour. The project also ships companion components (an Android tracer for enumerating Java classes/methods, and builders that produce "inspectable" APKs/IPAs for non-rooted Android and non-jailbroken iOS devices). Platforms are selected with `-p android`, `ios`, `iossim` or `macos` (Android needs adb/USB). Note the upstream repository is archived (2023) — it is unmaintained and targets the Frida versions of its era.

## Typical use cases

### List running processes on a device

Find the process or package name to target before attaching.

```
appmon -p android -ls 1
```

Outcome: connects Frida to the attached Android device and prints a PID/name table of every running process — the lookup step for choosing the `-a` argument.

### Monitor an app's API calls

Record what an app actually calls at runtime.

```
appmon -a com.example.app -p android -s agent_scripts/
```

Outcome: attaches Frida to the running `com.example.app` process, merges every `.js` file under `agent_scripts/` into one agent script, and logs intercepted API calls with timestamps into `./app_dumps/` (report database named after the app; `-o` and `-r` override the dump directory and database name). Add `--spawn 1` to launch the app fresh under instrumentation instead of attaching to a running instance.

*No corpus evidence — derived from upstream documentation.*
