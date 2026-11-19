---
name: "pextractor"
categories:
  - blackarch-windows
version: "0.18b"
homepage: "http://packetstormsecurity.com/files/62977/PExtractor_v0.18b_binary_and_src.rar.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/PExtractorWinSec"
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

# pextractor

> PExtractor — a small forensics utility that carves the original files back out of a Windows executable produced by a file joiner.

## What it's for

Malware of the 2000s was routinely assembled with "joiner" utilities that bind several files (dropper + payload, sometimes with dropper-stub packing) into one executable; at runtime the joined binary drops and runs its hidden components. PExtractor (v0.18b, distributed via Packet Storm as a RAR containing `PExtractor v0.18b.exe` and its C source — `main.c`, `funciones.h`, `ColaFicheros.h`) is the answering forensic tool: given a joiner-produced executable it scans the file, locates the embedded/attached payloads and extracts each one to disk for separate analysis. Reach for it during static triage of a suspicious binary that looks larger than its compiled code should be, or when a sandbox run shows files being dropped that the executable's resources list does not explain. It targets classic joiner artifacts and predates modern packers; it is not a general-purpose unpacker, carver for arbitrary container formats, or memory dumper. Its distribution page is bot-walled and the archive's CLI is not independently documented anywhere reachable, so no verified command examples can be given; there is no canonical GitHub repository (same-named GitHub projects are unrelated).

GitHub: third-party mirror of the Packet Storm PExtractor distribution (Seabreg/PExtractorWinSec).

*No corpus evidence — derived from upstream documentation.*
