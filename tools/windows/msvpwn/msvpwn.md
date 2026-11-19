---
name: "msvpwn"
categories:
  - blackarch-windows
version: "65.328921b"
homepage: "https://bitbucket.org/mrabault/msvpwn"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-03"
meta-checked: "2026-11-09"
github-repo: "Seabreg/msvpwn"
github-stars: 1
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# msvpwn

> Binary-patches msv1_0.dll so the Windows authentication check accepts any password — a local-authentication bypass for lab use.

## What it's for

msvpwn is a small C utility (MIT-licensed, OpenSSL dependency) that patches the Microsoft authentication DLL `msv1_0.dll` to completely disable its password verification: once a patched copy is in place, interactive logon succeeds with every conceivable password. It exists to demonstrate how thin the local password check is once you can modify the DLL that performs it — physical-access and lab scenarios, forensics on seized machines, or CTF-style practice boxes.

It operates on a file you supply, not on the live system: you point it at a copy of `msv1_0.dll`, it flips the verification logic in the binary (a per-version signature table drives the patching), and you place the patched file yourself. Running it again on the same file reverts the patch. New Windows-version signatures are added by editing the `PatchInfo` array in `src/config.h`. The original Bitbucket upstream is gone; the referenced GitHub repository carries the same tool with full README and man page.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Check a DLL copy's patch state

```
msvpwn -s msv1_0.dll
```

Outcome: with `-s` (status) the tool only reports whether the supplied DLL is patched or unpatched and exits — use it to verify which copy you are holding before and after patching, with no modification performed.

### Toggle the password check off (or back on)

```
msvpwn msv1_0.dll
```

Outcome: patches the supplied `msv1_0.dll` if it is unpatched, or reverts it if already patched. After the patched copy replaces the system DLL and the machine reboots, any password is accepted for local interactive logon — the toggle behaviour makes the same command undo the change.
