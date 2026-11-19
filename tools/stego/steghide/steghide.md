---
name: "steghide"
categories:
  - crypto-stego
version: "0.5.1+git20240220-2"
homepage: "https://steghide.sourceforge.net/"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur, nixpkgs]
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "StegHigh/steghide"
github-stars: 788
github-pushed: "2024-02-20"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# steghide

> Hide data in JPEG, BMP, WAV and AU files with compression and encryption.

## What it's for

Steghide, by Stefan Hetzl, embeds secret data into JPEG, BMP, WAV and AU
carriers without changing first-order color/sample statistics, making the
embedding resistant to basic statistical detection. Embedded data is
compressed, integrity-checked with a checksum, and encrypted with
Rijndael-128 in CBC mode using a passphrase. It remains the most commonly
encountered image stego tool on both sides: knowing a file came from
steghide, the passphrase is the only missing piece — which is exactly what
[[stegcracker]] and the much faster [[stegseek]] attack with wordlists.

## Typical use cases

### Extract data with a known passphrase

Pull the embedded file out of a stego carrier when the password is known.

```
steghide extract -sf cover.jpg -p Password1
```

Outcome: with the correct passphrase, extracts the embedded file to the current directory and prints `wrote extracted data to "<filename>"`; a wrong passphrase fails with an error and writes nothing.

### Embed a file in a cover file

Hide a data file inside an image or audio file.

```
steghide embed -cf cover.jpg -ef secret.txt
```

Outcome: prompts for a passphrase (asked twice), then embeds, compresses and encrypts `secret.txt` into `cover.jpg` in place, printing `embedding "secret.txt" in "cover.jpg"... done`.

### Inspect a cover or stego file

Check format, capacity and embedded-data details before extracting.

```
steghide info cover.jpg
```

Outcome: prints the file's format and embedding capacity (e.g. `capacity: 3.5 KB`); for a stego file it can additionally reveal the embedded file's name, size, encryption and compression — after supplying the passphrase when prompted.
