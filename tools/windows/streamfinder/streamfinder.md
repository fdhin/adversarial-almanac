---
name: "streamfinder"
categories:
  - blackarch-windows
version: "1.2"
homepage: "https://www.gaijin.at/en/dlstreamfind.php"
packaged-by: [blackarch]
blackarch-builddate: "2018-06-24"
build-system: windows
meta-checked: "2026-11-09"
github-repo: "Seabreg/streamfinder"
github-stars: 0
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# streamfinder

> Searches NTFS partitions or single folders for hidden Alternate Data Streams.

## What it's for

StreamFinder (Gaijin.at, v1.2, released 2008) searches for NTFS Alternate Data Streams (ADS) — the hidden file-within-a-file metadata that Windows Explorer and a plain `dir` do not show. ADS was a popular hiding place for 2000s-era malware and for smuggling data, because any file can carry arbitrarily named streams that ordinary listing tools ignore. The tool scans a complete partition or a single folder and reports the alternate streams it finds together with their host files, letting an examiner see what is hidden and handle it. It is a small freeware GUI utility: portable with no installer, from the Windows XP/Server 2003/Vista era, localized in German, English and Japanese. The current Gaijin.at catalog no longer carries it, so archived copies are the reference. On a modern system the equivalent check is PowerShell's `Get-Item -Stream *` (list) and `Remove-Item -Stream` (strip); StreamFinder remains useful on legacy hosts where only its era of tooling runs.

GitHub: third-party mirror of Gaijin.at StreamFinder (Seabreg/streamfinder).

*No corpus evidence — derived from upstream documentation.*
