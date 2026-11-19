---
name: "disitool"
categories:
  - blackarch-forensic
version: "0.4"
homepage: "https://blog.didierstevens.com/my-software/#disitool"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/disitool"
github-stars: 0
github-pushed: "2019-05-29"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# disitool

> Didier Stevens' tool to delete, copy, extract, add, and inject Authenticode digital signatures in PE files.

## What it's for

disitool manipulates the Authenticode digital signature embedded in Windows PE executables. It can strip a signature, extract it to a file, copy one executable's signature onto an unsigned file, re-add an extracted signature, or append arbitrary data inside a signed executable's signature area without invalidating the signature — the mechanism behind signature-abuse attacks and the reason oversized signature blobs deserve inspection. It is a single Python script (version 0.4 is Python 3) requiring the pefile module; the source is put in the public domain by Didier Stevens.

## Typical use cases

### Extract a signature from a signed executable

```
disitool extract program.exe program.sig
```

Outcome: writes the signature blob referenced by the PE security directory to `program.sig`; prints an error and produces nothing if the source file is not signed.

### Strip the signature from an executable

```
disitool delete program.exe program-unsigned.exe
```

Outcome: zeroes the security directory and truncates the file at the signature offset, producing an unsigned copy for further patching.

### Re-sign an unsigned file with an existing signature

```
disitool add program.sig patched.exe patched-signed.exe
```

Outcome: appends the stored signature to the unsigned PE and points its security directory at it. `copy` does the same in one step, taking the signature directly from another signed executable: `disitool copy original.exe patched.exe patched-signed.exe`.

### Hide data inside a validly signed executable

```
disitool inject --paddata signed.exe payload.bin signed-with-data.exe
```

Outcome: appends the payload inside the signature area and enlarges the signature accordingly, so the executable still carries (and hashes against) its original signature; `--paddata` zero-pads the payload to a multiple of 8 bytes to keep the structure aligned.

*No corpus evidence — derived from upstream documentation.*
