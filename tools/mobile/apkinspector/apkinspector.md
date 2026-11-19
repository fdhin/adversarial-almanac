---
name: "apkinspector"
categories:
  - mobile
homepage: "https://github.com/erev0s/apkInspector"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
license: Apache-2.0
meta-checked: "2026-11-13"
github-repo: "erev0s/apkInspector"
github-match: "exact"
github-stars: 99
gh-checked: "2026-11-13"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# apkinspector

> Parses APK zip structure per the spec itself — lists, extracts and decodes even APKs built to evade standard unzip/analysis libraries.

## What it's for

apkInspector performs static inspection of Android APK files by implementing
the zip specification directly, the way Android's own installer reads them,
instead of relying on standard zip libraries. That makes it the tool of choice
for APKs that use evasion techniques — duplicate end-of-central-directory
records, unknown compression methods, filename collisions, tampered manifest
strings — which make conventional parsers (unzip, androguard's default paths,
apkleaks) fail or silently miss content. It lists entries from both local
headers and the central directory (discrepancies between the two are
themselves an evasion signal), extracts files, and decodes the binary
AndroidManifest.xml. It is a library as well as a CLI, and is used by other
Android tooling (androguard, medusa) for exactly these hard cases.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### List every entry in an APK from both zip views

Compare local-header vs central-directory listings to spot planted entries.

```
apkInspector -apk app.apk -la
```

Outcome: prints all files by name from both the central directory and the
local headers side by side; entries appearing in only one view are the
classic marker of APK tampering.

### Decode the AndroidManifest.xml

Turn the binary manifest into readable XML.

```
apkInspector -apk app.apk -m
```

Outcome: extracts the AndroidManifest.xml from the APK and prints it decoded
— permissions, activities, exported components — without needing apktool.

### Check an APK for static-analysis evasion techniques

Run the tampering-indicator analysis.

```
apkInspector -apk app.apk -a
```

Outcome: reports any of the known evasion indicators (multiple EOCD records,
unknown compression methods, empty-filename compressed entries, path
collisions, tampered StringCount, oversize strings, invalid data between
elements, unexpected manifest attributes) with the offending entries named.

### Extract a single file from an evasive APK

Pull one entry out when unzip chokes on the archive.

```
apkInspector -apk app.apk -f classes.dex -x
```

Outcome: writes the requested file (here `classes.dex`) to the current
directory, extracted the way Android's installer would read it.

### Extract everything the central directory knows about

Bulk-extract all entries.

```
apkInspector -apk app.apk -xa
```

Outcome: extracts every file detected in the central directory header into
the current directory for offline analysis.

### Export a listing to JSON

Machine-readable inventory of the APK contents.

```
apkInspector -apk app.apk -lc -e
```

Outcome: writes the central-directory listing (whatever the list flags
selected) to a JSON file suitable for diffing against a reference build of
the same app.

### Decode a standalone encoded manifest

Decode a manifest file already pulled out of an APK.

```
apkInspector -sm AndroidManifest.xml
```

Outcome: decodes a binary/encoded AndroidManifest.xml passed directly
(without an APK around it) and prints the readable XML.
