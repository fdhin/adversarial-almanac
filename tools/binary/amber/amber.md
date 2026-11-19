---
name: "amber"
categories:
  - blackarch-binary
version: "v3.1.r2.gf6eb2dc"
homepage: "https://github.com/EgeBalci/Amber"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-02-03"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "EgeBalci/Amber"
github-stars: 1449
github-pushed: "2024-02-22"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# amber

> Turns a native Windows PE into a position-independent, in-memory (reflective) payload.

## What it's for

Amber is a reflective PE loader generator: it takes an input PE (EXE, DLL, SYS) and emits a standalone, position-independent payload that maps the file into memory, resolves its Windows API imports and executes it without writing the original PE to disk. It is built for offensive payload deployment — keeping execution in memory to evade signature-based antivirus, IDS/IPS and application whitelisting. Generated payloads are encoded with the SGN encoder by default, resolve APIs via CRC32 hashing or IAT walking, can perform raw syscalls (x64 only) and self-erase after the payload is loaded. Amber packs payloads; it does not analyze or unpack binaries.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Pack a PE into a reflective payload

Convert an existing PE into a standalone in-memory payload.

```
amber -f payload.exe -o loader.bin
```

Outcome: writes `loader.bin`, a position-independent reflective loader that, when executed on Windows, maps and runs `payload.exe` entirely in memory; the payload is SGN-encoded by default.

### Multi-round encoding with IAT API resolution

Harden the payload against static detection with repeated SGN encoding and import-table-based API resolution.

```
amber -e 10 --iat -f payload.exe
```

Outcome: generates the reflective payload using the IAT API resolver block instead of the CRC32 resolver, with the final payload encoded 10 times.

### Strip PE artifacts and use raw syscalls

Remove PE header hints and avoid standard API surfaces on x64.

```
amber --sys --scrape -f payload.exe
```

Outcome: generates a payload that scrapes the PE magic bytes and DOS stub from the in-memory image and reaches Windows functionality through raw syscalls (x64 only).
