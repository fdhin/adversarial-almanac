---
name: "drozer"
categories:
  - blackarch-mobile
version: "2.4.4"
homepage: "https://github.com/ReversecLabs/drozer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "ReversecLabs/drozer"
github-stars: 4649
github-pushed: "2026-04-08"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 28
gh-activity-checked: "2026-11-09"
---

# drozer

> The classic Android security-testing framework: a console that acts as an app on the device and probes every IPC surface.

## What it's for

drozer (formerly Mercury) tests Android apps and devices for vulnerabilities by
assuming the role of an application and interacting with the Dalvik VM, other
apps' IPC endpoints (activities, services, content providers, broadcast
receivers) and the underlying OS — so the impact of exported components can be
shown without writing a custom attack app. It runs as two halves: the drozer
Agent app on the device embeds a server, and the `drozer` console on the
workstation drives it through modules (`run app.package.list`, `run
app.activity.start`, ...); inside a session `list` enumerates available modules
and `shell` drops to a device shell. It originated at MWR InfoSecurity, passed
through F-Secure/WithSecure, who ended feature development, and the repository
now lives under Reversec Labs; it is a Python 2.7-era tool, and modern use is
easiest through the published Docker image. Expect parts of the module set to
be inapplicable on current Android releases.

## Typical use cases

### Start a session against a USB-connected device

Install the agent, forward its port, and open the console.

```
adb install drozer-agent-2.3.4.apk
adb forward tcp:31415 tcp:31415
drozer console connect
```

Outcome: after enabling the agent's "Embedded Server" on the device, the
console opens a `dz>` prompt and prints the connected device's Android ID.

### Connect to a device over the network

Reach an agent on a device that is not USB-attached.

```
drozer console connect --server 198.51.100.10
```

Outcome: same `dz>` prompt, tunneled to the agent at 198.51.100.10 (default
port 31415).

### Enumerate installed packages

Find target packages by keyword.

```
run app.package.list -f browser
```

Outcome: prints the identifiers of apps whose package name matches `browser`,
ready to feed to the info, manifest and attacksurface modules.

### Inspect a package's attack surface

See which components an app exports and what guards them.

```
run app.package.attacksurface com.example.app
```

Outcome: reports the app's exported activities, services, receivers and
content providers plus its declared permissions — the shortlist for deeper
probing.

### Read an app's APK manifest

Dump the manifest of an installed package as the device sees it.

```
run app.package.manifest com.example.app
```

Outcome: prints the package's AndroidManifest — permissions, intent filters
and exported flags — without needing the APK file itself.

### Launch an exported activity

Fire an intent at a component to prove it is reachable from any app.

```
run app.activity.start --component com.example.app com.example.app.MainActivity
```

Outcome: the device starts the named activity; a crash or launch confirms the
component is exported and callable by a third-party app.

### Query an exposed content provider

Read rows from a provider that lacks access control.

```
run app.provider.query content://com.example.app.provider/items
```

Outcome: dumps the provider's table through the console's formatter
(`--selection`, `--projection` and `--order` narrow the query like SQL),
exposing whatever data the app serves without permission checks.

*No corpus evidence — derived from upstream documentation.*
