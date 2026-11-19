---
name: "stegosuite"
categories:
  - crypto-stego
version: "0.9.4-1"
homepage: "https://codeberg.org/tob/stegosuite"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: java
meta-checked: "2026-11-09"
github-repo: "osde8info/stegosuite"
github-stars: 50
github-pushed: "2026-01-28"
github-archived: false
github-issues: 10
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stegosuite

> Java GUI for hiding messages and files in images with AES encryption.

## What it's for

Stegosuite is a free Java steganography tool (upstream now on Codeberg; the
GitHub repo recorded above is its previous home) that hides text messages and
multiple files of any type inside BMP, GIF, JPG and PNG images. Embedded data
is AES-encrypted under a passphrase, and the embedding automatically avoids
homogenous image areas, writing only into noisy regions where per-pixel
changes are hardest to notice. It is a desktop application driven entirely
through its GUI — the packaged `stegosuite` launcher (or `java -jar` on the
jar) opens it; there is no scriptable command-line mode.

## Typical use cases

### Launch the Stegosuite GUI

Open the application to embed or extract data in an image.

```
stegosuite
```

Outcome: opens the Stegosuite window. To embed, open an image, add the message and/or files plus a passphrase and save the stego image; to extract, open the stego image in the same interface and re-enter the passphrase to recover the hidden message and files.

*No corpus evidence — derived from upstream documentation.*
