---
name: "sign"
categories:
  - blackarch-mobile
version: "10.2dc4018"
homepage: "https://github.com/appium/sign"
packaged-by: [blackarch]
blackarch-builddate: "2020-05-06"
build-system: java
license: Apache
meta-checked: "2026-11-09"
github-repo: "appium/sign"
github-stars: 429
github-pushed: "2020-03-18"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sign

> Re-sign an APK with the bundled Android test certificate in one step.

## What it's for

After patching and recompiling an APK (e.g. with apktool) the original
signature no longer matches and Android refuses to install it. This tool
(originally from the Appium project, based on AOSP's `SignApk.java`) embeds
the public/private Android test key pair inside a single jar, so
re-signing a modified APK needs no key generation and no SDK build tools.
It is strictly a test-certificate signer for lab and instrumentation work:
every APK it produces shares the well-known public test key, so it must
never stand in for release signing.

## Typical use cases

### Sign a modified APK

Make a patched APK installable again.

```
sign target.apk
```

Outcome: writes `target.s.apk` next to the input, signed with the embedded
test certificate — the equivalent of running AOSP SignApk with
`testkey.x509.pem`/`testkey.pk8` (`java -jar sign.jar` under the hood).

### Replace the APK in place

Avoid a second file when the original is disposable.

```
sign target.apk --override
```

Outcome: `target.apk` itself is overwritten with the signed version instead
of producing a `target.s.apk` sibling.

### Verify the produced signature

Confirm the JAR-level signature before installing.

```
jarsigner -verify target.s.apk
```

Outcome: `jarsigner` (from any JDK) reports `jar verified`, confirming the
APK's ZIP entries are covered by the test-certificate signature.

*No corpus evidence — derived from upstream documentation.*
