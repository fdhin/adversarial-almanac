---
name: "img4tool"
categories:
  - mobile
homepage: "https://github.com/tihmstar/img4tool"
origin: "hacktricks"
recipe: "upstream"
packaged-by:
  - nixpkgs
build-system: autotools
meta-checked: "2026-11-13"
github-repo: "tihmstar/img4tool"
github-stars: 337
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "see repository"
---

# img4tool

> Manipulates Apple IMG4, IM4M and IM4P images: print, extract, create, rename and verify the signed firmware containers used by iOS boot components and SHSH blobs.

## What it's for

`img4tool` works on the IMG4 container family that Apple wraps firmware in: full `IMG4` images, `IM4M` manifests (the APTicket/signature part of SHSH blobs) and `IM4P` payloads (the encrypted or raw firmware itself, e.g. kernelcaches and iBSS). It prints their structure, extracts IM4P/IM4M from images and SHSH files, extracts and — given the per-device IV/key — decrypts the payload, builds new IMG4 files from components, changes an IM4P's type tag, converts SHSH blobs to IM4M, and verifies that a ticket is valid for a given BuildManifest. It is the companion tool to jailbreak/downgrade workflows (checkm8-era) and to inspecting boot-chain files pulled from IPSWs; it does not sign anything with Apple keys — verification only checks existing signatures.

## Typical use cases

### Print an image's structure

```
img4tool infile.img4
img4tool -a ticket.im4m
```

Outcome: decodes the container and prints its elements; `-a` prints everything from the IM4M manifest (board, nonce, generator, certificate chain) — the quick triage of a saved blob before using it.

### Extract components from an IMG4 or SHSH

```
img4tool -e -p out.im4p in.img4
img4tool -e -m out.im4m in.img4
img4tool -e -p out.im4p -s in.shsh
```

Outcome: writes the IM4P payload or IM4M manifest of the image (or of an SHSH blob via `-s`) to separate files for further processing.

### Extract and decrypt the payload

```
img4tool -e --iv <IV> --key <KEY> -o out.bin in.im4p
```

Outcome: pulls the payload out of the IM4P and decrypts it with the supplied per-device IV/KEY (as published in firmware-keys databases), yielding the raw firmware binary (e.g. a usable kernelcache) in `out.bin`.

### Create an IMG4 from components

```
img4tool -c out.img4 -p in.im4p -m in.im4m
img4tool -c out.img4 -p in.im4p -s in.shsh
```

Outcome: assembles a new signed IMG4 by combining a payload with a manifest — taken directly from an IM4M or from inside an SHSH blob (`-s`) — the rebuild step of downgrade image preparation.

### Build a custom IM4P

```
img4tool -c out.im4p -t ibss -d "Pwned iBSS no sigpatches" IBSS.raw
```

Outcome: wraps a raw binary (here an iBSS) as an IM4P with type tag `ibss` and a custom description string, ready to be booted or embedded in an IMG4.

### Change an image's type tag

```
img4tool -n rkrn -p kernel.im4p
```

Outcome: renames the IM4P's type in place (e.g. `krnh`→`rkrn`), the trick for making boot loaders treat a kernel image differently — used when repurposing firmware components.

### Verify a ticket against a BuildManifest

```
img4tool --verify BuildManifest.plist ticket.im4m
img4tool --verify BuildManifest.plist in.img4
```

Outcome: checks (exit status plus messages) whether the IM4M/SHSH (`-s ticket.shsh` variant) or a complete IMG4 is correctly signed for the given manifest — i.e. whether a saved blob actually matches the firmware being targeted before an attempt is made to restore with it.

*No corpus evidence — derived from upstream documentation.*
