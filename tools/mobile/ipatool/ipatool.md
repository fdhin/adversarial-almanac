---
name: "ipatool"
categories:
  - mobile
homepage: "https://github.com/majd/ipatool"
origin: "pentest-book"
recipe: "upstream"
packaged-by: [aur, nixpkgs]
build-system: go
meta-checked: "2026-11-13"
github-repo: "majd/ipatool"
github-stars: 12313
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: MIT
---

# ipatool

> Search, license and download App Store packages (`.ipa`/`.pkg`) for iOS and macOS from the terminal.

## What it's for

ipatool (by Majd Alfhaily) is a Go CLI that speaks the App Store's own
protocols: after authenticating with a regular Apple Account it can
search the App Store catalog, "purchase" (license) free apps, and
download the exact encrypted `.ipa` (or macOS `.pkg`) package that
Apple distributes, together with per-version metadata. For mobile
security work its value is procurement — it pulls the shipped binary
for offline inspection (unzipping the IPA, static analysis) without a
device, iTunes or Apple Configurator. It does not defeat FairPlay/DRM:
the downloaded payload stays encrypted, and decryption requires
separate tooling on a jailbroken device. Most commands need a prior
`ipatool auth login`; `--format json` and `--non-interactive` make runs
scriptable.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Log in with an Apple Account

Establish the App Store session every other command needs.

```
ipatool auth login
```

Outcome: interactively prompts for the Apple ID and password (plus the
2FA code when enabled) and caches the App Store session in the OS
keychain, so subsequent commands run without re-authentication.

### Search the App Store catalog

Find an app's bundle identifier and numeric ID before downloading.

```
ipatool search "example app" --limit 10
```

Outcome: queries the App Store and prints up to 10 matches with their
bundle identifiers, numeric app IDs and prices; add
`--format json` to consume the results from a script.

### License and download an app package

Pull the distributed `.ipa` for offline inspection.

```
ipatool download --bundle-identifier com.example.app --output app.ipa --purchase
```

Outcome: licenses the app if the account does not own it yet
(`--purchase`), downloads the latest version's encrypted package to
`app.ipa`, and prints progress; unzip the `.ipa` to inspect the
bundled `Payload/` directory.

### Download a specific app version

Fetch a pinned historical build.

```
ipatool download -b com.example.app --external-version-id 123456789 -o old.ipa
```

Outcome: resolves the external version ID (found via
`ipatool list-versions -b com.example.app`) and downloads that exact
build instead of the latest one.

### Dump a version's metadata

Read what Apple knows about one release.

```
ipatool get-version-metadata -b com.example.app --external-version-id 123456789
```

Outcome: prints the stored metadata for that release (version string,
upload dates, supported devices and similar fields) without
downloading the package; useful to map version IDs before pulling
binaries.
