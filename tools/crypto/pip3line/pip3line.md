---
name: "pip3line"
categories:
  - blackarch-crypto
version: "92.5e27195"
homepage: "https://github.com/nccgroup/pip3line"
packaged-by: [blackarch]
blackarch-builddate: "2017-12-07"
build-system: cmake
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/pip3line"
github-stars: 43
github-pushed: "2016-10-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pip3line

> Raw bytes manipulation utility with a Qt GUI and a CLI (pip3linecmd): apply, chain, and reverse encodings, hashes, compression, and dozens of security-specific transforms.

## What it's for

pip3line is a swiss-army knife for transforming bytes: Base64, hexadecimal, URL encoding, zlib, hashing (MD5/SHA families), crypto, and many specialized security decoders (Cisco secret 7, MySQL formats, byte XOR, Microsoft timestamps, and more). The Qt application gives interactive side-by-side inbound/outbound views where transforms can be chained, compared, and searched within the raw bytes; the companion CLI `pip3linecmd` runs a single transform or a whole chain exported from the GUI over stdin. It is a data triage and conversion workbench — it transforms data you already have, it does not attack anything by itself. Development started at NCC Group and continues upstream as metrodango/pip3line (Qt5); the packaged nccgroup-era build carries the same GUI/CLI split.

## Typical use cases

### Encode input through a transform on the command line

```
echo -n 'Hello world' | pip3linecmd -t Base64
```

Outcome: applies the `Base64` transform in the inbound (encode/encrypt) direction and prints `SGVsbG8gd29ybGQ=`; substitute any registered transform name (`Hexadecimal`, `Url Encode`, `Zlib`, ...).

### Decode or decrypt in the outbound direction

```
echo 'SGVsbG8gd29ybGQ=' | pip3linecmd -t Base64 -o
```

Outcome: `-o` runs the transform in the outbound (decode/decrypt) direction and prints the recovered bytes `Hello world` — the same switch reverses hex, URL encoding, zlib, and the crypto transforms.

### Process input as one binary block

```
cat sample.bin | pip3linecmd -t Zlib -o -b
```

Outcome: `-b` treats stdin as a single binary block instead of a text file of lines, and the zlib transform prints the decompressed stream — the right mode for blobs containing NUL bytes or arbitrary binary.

### Replay a transform chain saved from the GUI

```
cat sample.bin | pip3linecmd -f chain.conf -b
```

Outcome: applies the full chain of transforms configured and saved from a GUI session (`-f` takes the exported configuration file) — useful for repeatedly triaging the same protocol format without rebuilding the chain by hand.

### Explore transforms interactively in the GUI

```
pip3line
```

Outcome: opens the Qt application — paste bytes into the input pane, pick transforms from the list to watch the live output, chain multiple steps, search within the raw bytes, and export the chain as a configuration file for `pip3linecmd`.

*No corpus evidence — derived from upstream documentation.*
