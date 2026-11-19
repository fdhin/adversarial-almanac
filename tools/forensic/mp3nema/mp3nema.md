---
name: "mp3nema"
categories:
  - blackarch-forensic
version: "0.4"
homepage: "http://packetstormsecurity.com/files/76432/MP3nema-Forensic-Analysis-Tool.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2018-11-15"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "enferex/mp3nema"
github-stars: 9
github-pushed: "2013-07-01"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mp3nema

> MP3 forensic tool that detects, extracts and injects "out-of-band" data hidden between MP3 frames.

## What it's for

MP3 frames do not have to sit adjacently — data can be smuggled into the gaps between them. mp3nema (by Matt Davis, "enferex", of 757 Labs) analyzes MP3 files and streams for such out-of-band data, extracts it to a file, and can conversely inject a file's data between frames. It can also capture an MP3 stream from an HTTP URL, following redirects. Use it to investigate steganography or covert channels distributed as audio, or to embed test payloads when validating detection. It is a small single-purpose C utility operating at the frame layer; it does not decode audio or parse ID3 metadata.

## Typical use cases

### Extract hidden out-of-band data from an MP3

Recover data smuggled between the frames of an MP3 file.

```
mp3nema track.mp3 -e
```

Outcome: parses the frame structure, locates the out-of-band bytes between frames, and writes the recovered data to a file for inspection.

### Inject a payload between MP3 frames

Embed a file's contents into the inter-frame gaps, for example to test detection tooling.

```
mp3nema carrier.mp3 -i payload.txt
```

Outcome: places `payload.txt`'s data between the frames of the MP3 while leaving the audio playable; the `-i` argument may also name a directory of MP3 carriers.

### Capture and analyze a network MP3 stream

Pull an HTTP MP3 stream while checking it for inter-frame data.

```
mp3nema -c http://stream.example.com:8000/live.mp3
```

Outcome: connects to the stream URL (following redirects), captures the audio to disk, and reports any out-of-band data found between frames.

### Verbose frame-level analysis

See the frame structure and where out-of-frame data sits without extracting.

```
mp3nema track.mp3 -v
```

Outcome: prints detailed frame-by-frame information, including the out-of-frame data encountered — enough to confirm a file is a carrier.

*No corpus evidence — derived from upstream documentation.*
