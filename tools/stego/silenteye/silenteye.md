---
name: "silenteye"
categories:
  - blackarch-stego
version: "21.a53a7ff"
homepage: "https://github.com/achorein/silenteye/"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-20"
build-system: cmake
license: GPL3
meta-checked: "2026-11-09"
github-repo: "achorein/silenteye"
github-stars: 149
github-pushed: "2023-03-06"
github-archived: false
github-issues: 47
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# silenteye

> Cross-platform Qt GUI for hiding messages and files in images.

## What it's for

SilentEye is a C++/Qt desktop application (Linux, Windows, macOS) that makes
image steganography point-and-click: open a carrier image, type a message or
attach a file, optionally set a password, and encode; the same interface
decodes data hidden by SilentEye elsewhere. Supported formats and encoding
schemes come from loadable plugins (the default works on images such as BMP
and JPEG), and hidden data can be password-protected. It is GUI-only — the
packaged `silenteye` binary is the application launcher, not a scriptable
command-line interface.

## Typical use cases

### Launch the SilentEye GUI

Start the application to hide or recover data in images.

```
silenteye
```

Outcome: opens the main SilentEye window. To hide data, choose an image, enter the message or file and an optional password in the encode panel and confirm; to recover, load the stego image in the decode panel and re-enter the same password to get the hidden content back.

*No corpus evidence — derived from upstream documentation.*
