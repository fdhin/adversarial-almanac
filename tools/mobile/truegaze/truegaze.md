---
name: "truegaze"
categories:
  - blackarch-mobile
version: "117.c3f26bc"
homepage: "https://github.com/nightwatchcybersecurity/truegaze"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "nightwatchcybersecurity/truegaze"
github-stars: 133
github-pushed: "2023-08-24"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# truegaze

> Plugin-based static analyzer that finds security issues in Android/iOS apps outside the source code — SDK misconfigurations, exposed Firebase data, weak signing keys.

## What it's for

truegaze (Nightwatch Cybersecurity) statically analyzes compiled mobile applications, targeting problems that live outside the application source: resource strings, third-party library configuration files, and signing material. Scanning is plugin-based — AdobeMobileSdk detects missing SSL enforcement in Adobe Mobile SDK config, FirebasePlugin detects insecurely configured Firebase databases and GCP storage buckets referenced by the app, and WeakKeyPlugin flags weak Android signing keys (e.g. well-known debug keys). Each plugin declares whether it applies to Android, iOS, or both.

Most checks are offline; a few consult live services via `--online`. Results print to the terminal only (no report file — JSON/XML export was on the wishlist, never built). The upstream repository is archived, so expect no further plugins; it still runs under Python 3.

## Typical use cases

### List the available plugins

See what checks this build ships before scanning.

```
truegaze list
```

Outcome: prints the total active plugin count and a table of each plugin's name, description, and whether it applies to Android and/or iOS.

### Scan an Android application

Check an APK for configuration-level weaknesses.

```
truegaze scan app.apk
```

Outcome: identifies the file as an Android app, runs every Android-capable plugin, and prints a `FOUND:` line per finding (for example non-SSL analytics URLs in an Adobe SDK config or a hardcoded Firebase database URL), ending with `Done!`.

### Scan an iOS application

Run the same pass over an IPA archive.

```
truegaze scan app.ipa
```

Outcome: identifies the app as iOS via `Payload/*.app/Info.plist` and runs only the iOS-capable plugins, reporting findings the same way.

### Include checks that need network access

Enable the online plugin set on top of the offline ones.

```
truegaze scan app.apk --online
```

Outcome: additionally runs the plugins that query live services (used to confirm that a discovered Firebase instance is actually exposed); use only against applications you are authorized to assess.

*No corpus evidence — derived from upstream documentation.*
