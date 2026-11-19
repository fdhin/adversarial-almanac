---
name: "analyzepesig"
categories:
  - blackarch-windows
version: "0.0.0.5"
homepage: "https://blog.didierstevens.com/my-software/#AnalyzePESig"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "DidierStevens/AnalyzePESig"
github-stars: 19
github-pushed: "2023-08-03"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# analyzepesig

> Didier Stevens' Windows console tool that reports the Authenticode signature state of PE files — from signature presence to the full PKCS#7 certificate chain.

## What it's for

AnalyzePESig parses the embedded digital signature of a PE file and reports what it finds: signature size and revision, the PKCS#7 parse result, bytes trailing the signature, signing and counter-signature timestamps, the signer's subject/issuer chain, thumbprints, hash algorithms and catalog-file information. It is one of the few Didier Stevens tools written as native C++ rather than a Python script from the [[didier-stevens-suite]] (his own GitHub mirror carries the Visual Studio project; the canonical distribution is the zip on his software page). Reach for it when triaging a directory of executables and needing to know which are signed, by whom, and whether the embedded signature actually parses — including per-file properties such as MD5 and entropy in CSV mode. BlackArch installs the prebuilt console executables (AnalyzePESig-x86.exe, AnalyzePESig-x64.exe) under /usr/share/windows/analyzepesig/, where they run under Wine.

## Typical use cases

### Check a single binary

```
AnalyzePESig-x64.exe signed.exe
```

Outcome: prints the per-file signature analysis for signed.exe — signature size and revision, PKCS#7 parse result, signer subject and issuer names, thumbprint, and signature/counter-signature timestamps.

### Inventory a directory tree as CSV

```
AnalyzePESig-x64.exe -v -s -e -o report.csv C:\tools\
```

Outcome: walks C:\tools\ including subdirectories (-s), analyzes only files that are actual executable images regardless of extension (-e), and writes report.csv with one row per file covering hash, entropy and timestamps plus the full set of signature and certificate-chain fields — a quick signed/unsigned census of a whole host.

### Offline analysis with XML output

```
AnalyzePESig-x64.exe -r -x signed.exe
```

Outcome: produces an XML report of the same signature fields while skipping certificate revocation checks (-r), which suits an isolated analysis machine with no CRL/OCSP access. Targets can also be passed indirectly: a leading @ names a file listing one target per line.

*No corpus evidence — derived from upstream documentation.*
