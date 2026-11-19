---
name: "zipexec"
categories:
  - blackarch-crypto
version: "19.f8d661f"
homepage: "https://github.com/Tylous/ZipExec"
packaged-by: [blackarch]
blackarch-builddate: "2022-07-17"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "Tylous/ZipExec"
github-stars: 1031
github-pushed: "2022-07-01"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# zipexec

> Wraps a Windows binary in a password-protected zip carried by a JScript loader, executing it without ever decrypting it to disk.

## What it's for

ZipExec (Tyl0us, Go) is a proof-of-concept evasion wrapper. It takes a Windows executable, packs it into a password-protected zip, base64-embeds the zip into a generated JScript file, and that loader — when run with `wscript` on the target — rebuilds the zip on disk and launches the binary from inside it via COM objects (the Windows shell's password-protected-folder path), so the payload never exists on disk as an unencrypted, scanner-readable file. That defeats static EDR/AV scans and disk-based malware analysis of the dropped artifact, since zip contents protected by a password cannot be enumerated. It is a packaging tool: it produces the loader, it does not deliver it. The password-protected-zip technique is Windows-specific and does not apply to ELF payloads.

## Typical use cases

### Generate a JScript loader for a binary

```
zipexec -I payload.exe -O loader.js
```

Outcome: writes `loader.js` containing the base64-encoded password-protected zip; executing it with `wscript` on a Windows host rebuilds the zip, opens it via COM with the embedded password, and starts `payload.exe` without extracting or decrypting it to disk first.

### Add sandbox evasion to the loader

```
zipexec -I payload.exe -O loader.js -sandbox
```

Outcome: same loader, but it first checks `IsDomainedJoined` and only proceeds on hosts matching the evasion condition, reducing execution inside sandboxes and analysis VMs.

*No corpus evidence — derived from upstream documentation.*
