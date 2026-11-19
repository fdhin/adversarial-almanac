---
name: "jdeserialize"
categories:
  - blackarch-webapp
version: "31.20635ba"
homepage: "https://github.com/frohoff/jdeserialize/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-10-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "frohoff/jdeserialize"
github-stars: 36
github-pushed: "2014-09-19"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jdeserialize

> Interprets raw Java serialized streams into class declarations and instance values, without the original classes.

## What it's for

jdeserialize (originally by Brandon Creighton; the frohoff GitHub repository packages the canonical source) parses the output of Java's `ObjectOutputStream` — the raw bytes of Java serialization. It implements the Object Serialization Stream Protocol fully but never instantiates the classes inside the stream: it rebuilds an intermediate representation instead, so it can analyze captures whose class files you do not have. That makes it the standard companion for picking apart mystery `.ser` blobs, HTTP parameters such as `javax.faces.ViewState`, or captured deserialization-attack traffic: it prints compilable class declarations, textual dumps of instance values, and can carve out block data. It is an analysis tool, not an exploitation one — for turning a stream into a gadget attack use a dedicated exploitation toolkit.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Dump a serialized stream

Reconstruct what a captured serialization file contains.

```
jdeserialize session.ser
```

Outcome: prints reconstructed Java class declarations for every type in the stream followed by a textual representation of each instance's field values — enough to identify the class graph and spot gadget-style classes by name.

### Cut JDK noise out of the class dump

Exclude uninteresting package prefixes from the declaration output.

```
jdeserialize -filter 'java\..*' captured.ser
```

Outcome: class declarations for classes whose names match the regex (here the JDK's own `java.*` types) are suppressed, leaving only the application classes to read.

### Carve raw block data to a file

Extract the primitive data blocks written between object headers.

```
jdeserialize -blockdata blockdata.bin captured.ser
```

Outcome: the stream's raw block-data records are written to `blockdata.bin` for inspection with other tools; add `-blockdatamanifest <file>` alongside to also write a manifest describing each record's offset and length.
