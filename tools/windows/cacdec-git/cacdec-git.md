---
name: "cacdec-git"
categories:
  - windows
version: "20200309.r5"
homepage: "https://github.com/airbus-cert/cacdec"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "airbus-cert/cacdec"
github-stars: 27
github-pushed: "2020-03-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cacdec-git

> Decode the hidden RemoteFX frame recording that recent Windows RDP clients can write, recovering every frame the client displayed as BMP images.

*No corpus evidence — derived from upstream documentation.*

## What it's for

The official Microsoft RDP client (`mstsc.exe`) on Windows 10 and later hides a recorder: with two registry keys set on the client (`HKLM\SOFTWARE\Microsoft\Terminal Server Client\EnableRecording` DWORD `1`, and `RecordingPath` pointing at an output file), it writes the frames of the RemoteFX graphics channel to disk. That recording is a forensic artifact of what was seen on the client side of a session. cacdec parses such a recorded stream and dumps its frames as `.bmp` files, doing for the RemoteFX channel what bitmap-cache parsers (e.g. bmc-tools) do for the main RDP bitmap cache. It works purely offline on an already-recorded stream; it captures nothing off the wire, and it only helps when the recorder was enabled and both ends of the session negotiated RemoteFX.

## Typical use cases

### Convert a recorded RDP session into BMP frames

```
cacdec -i recording.bin -o /tmp/frames
```

Outcome: parses the RemoteFX recorder stream and writes each decoded frame as an individual BMP file into the output directory, giving a frame-by-frame reconstruction of the remote desktop as the client rendered it.
