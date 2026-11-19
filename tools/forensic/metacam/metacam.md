---
name: "metacam"
categories:
  - forensics
version: "1.2-15"
homepage: "http://www.cheeseplant.org/~daniel/pages/metacam.html"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: other
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# metacam

> EXIF and maker-note metadata reader for digital camera files by Daniel Sthepens — decodes standard Exif plus Nikon, Olympus, Canon and Casio vendor fields.

*No corpus evidence — derived from upstream documentation.*

## What it's for

metacam reads and decodes meta-information from digital camera images containing Exif. It was originally written to reverse-engineer files from the Nikon D1, and decodes a good chunk of the vendor-specific fields that generic Exif readers skip — Nikon (the original focus), Olympus, Canon and Casio maker data are all supported, and unknown tags can be shown raw instead of being silently dropped. It is written in C++ and built in a general, not Nikon-specific manner, so it works across Exif-carrying cameras. It only reads and prints metadata — it does not edit or strip tags. In forensic triage it answers provenance questions about image evidence: camera model, lens, exposure settings and embedded timestamps.

## Typical use cases

### Read all metadata from a camera image

```
metacam DSC_0001.jpg
```

Outcome: prints the decoded Exif and vendor fields for the file — camera and lens model, exposure settings, timestamps, and any decoded Nikon/Olympus-specific fields.

### Include unknown and vendor-undocumented tags

```
metacam -v DSC_0001.jpg
```

Outcome: also displays tags metacam cannot name, so undocumented vendor entries appear as raw values instead of being skipped; `-a` goes further and displays all tags, implying `-v`.

### Emit machine-readable XML

```
metacam -x DSC_0001.jpg > DSC_0001.xml
```

Outcome: writes the same decoded metadata as an XML document (added in v1.0), suitable for diffing two images' settings or feeding an automated processing pipeline.

### Process a batch of images

```
metacam *.jpg
```

Outcome: iterates over every matching file, printing each file's decoded metadata in turn — quick triage of a whole image set.
