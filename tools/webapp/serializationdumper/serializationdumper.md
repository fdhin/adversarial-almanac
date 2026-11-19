---
name: "serializationdumper"
categories:
  - blackarch-webapp
version: "34.6d161cd"
homepage: "https://github.com/NickstaDB/SerializationDumper/"
packaged-by: [blackarch]
blackarch-builddate: "2024-06-30"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "NickstaDB/SerializationDumper"
github-stars: 1081
github-pushed: "2024-06-21"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# serializationdumper

> Dump Java serialization streams (and Java RMI packet contents) into an annotated, human-readable form — and rebuild them.

## What it's for

SerializationDumper decodes the raw Java object serialization stream format (`ac ed 00 05` magic
and everything after it) into a field-by-field breakdown: class descriptors, serialVersionUIDs,
field types and values, back-references and block data. It works without the application's
classes on the classpath because it parses the stream structure instead of deserializing the
objects, which makes it safe to point at hostile data. This is the standard companion when
analyzing Java deserialization vulnerabilities: identify what gadget classes a stream references,
then tamper with values and rebuild a working stream. It also parses Java RMI packet contents and
can convert dumped text back into a binary stream.

## Typical use cases

### Decode a hex-encoded stream pasted on the command line

```
serializationdumper aced0005740004414243447071007e0000
```

Outcome: prints the stream token by token — `STREAM_MAGIC`, `TC_STRING` with its length and value,
`TC_NULL`, `TC_REFERENCE` with handle numbers — so the object graph and the classes it names are
readable without the original code.

### Dump a raw captured stream from a file

```
serializationdumper -r request_body.bin
```

Outcome: the binary file (e.g. a captured HTTP request body or RMI packet payload) is parsed and
printed in the same annotated form; `-f` does the same for a file of hex-ascii encoded bytes.

### Edit a dump and rebuild a binary stream

```
serializationdumper -b dumped.txt rebuilt.bin
```

Outcome: a previously dumped (and hand-modified) text dump — changed string lengths, values or
field bytes — is recompiled into a valid binary serialization stream in `rebuilt.bin`, ready to
be replayed at the target.

No corpus evidence — derived from upstream documentation (README).
