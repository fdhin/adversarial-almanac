---
name: "cminer"
categories:
  - blackarch-binary
version: "25.d766f7e"
homepage: "https://github.com/EgeBalci/Cminer/"
packaged-by: [blackarch]
blackarch-builddate: "2017-06-28"
build-system: other
license: MIT
meta-checked: "2026-11-09"
github-repo: "EgeBalci/Cminer"
github-stars: 154
github-pushed: "2023-04-07"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cminer

> Enumerates code caves in PE files — contiguous runs of unused bytes — with address range, size, file offset, and owning section.

## What it's for

cminer scans the sections of a Windows PE binary and reports every contiguous run of free bytes (a "code cave") at least as large as the threshold you set: the padding and zero/int3 gaps where code or data can be hidden or injected without changing the file's size. For each cave it prints the start and end addresses, the size, the file offset, and the section that contains it — exactly the information needed to plan an in-place patch or to gauge how much room a sample leaves for embedded payloads. It only inventories space; it does not write to the file, and it targets PE files only.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Find caves above a chosen size threshold

```
cminer sample.exe 200
```

Outcome: prints "[*] Minimum cave size set to 200" followed by every cave of at least 200 bytes with its address range, size, file offset, and containing section — the candidate locations for payload injection or hidden-data checks.

### Survey a sample with the default threshold

```
cminer sample.exe
```

Outcome: same report with the minimum cave size defaulting to 300 bytes (values below 100 are rejected as too small), giving a quick overview of the largest free regions in the image.
