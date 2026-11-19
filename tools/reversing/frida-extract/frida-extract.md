---
name: "frida-extract"
categories:
  - blackarch-reversing
version: "13.abb3f14"
homepage: "https://github.com/OALabs/frida-extract"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "OALabs/frida-extract"
github-stars: 114
github-pushed: "2017-03-02"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# frida-extract

> Frida-based extractor for PE payloads injected into other processes via RunPE or MapViewOfSection.

## What it's for

Unpacking malware that hides its payload with process injection. `FridaExtract.py` launches the sample under Frida, hooks the Windows API calls that implement RunPE and MapViewOfSection injection (`NtWriteVirtualMemory`, `NtMapViewOfSection`, `NtCreateSection`, `NtCreateThread`, `NtResumeThread`, ...), and reconstructs the injected PE from the memory written into the child process. Reach for it when a sandbox trace of a packed sample shows the classic sequence — `CreateProcess`, a remote write (`WriteVirtualMemory` or `NtMapViewOfSection`), then `ResumeThread` — which is the final stage of many packers. It is deliberately limited: it only works on 32-bit Windows hosts (Windows 7 / Server 2008 era), it is not stealthy (Frida injects a JavaScript runtime that malware can detect), and its userland hooks are bypassable. The source is intentionally written as a template for building your own Frida hook projects.

## Typical use cases

### Dump an injected PE from a packed sample

Run the sample under the extractor and let it reconstruct the hidden PE automatically.

```
python FridaExtract.py bad.exe
```

Outcome: the sample is started under Frida, the PE injected into the child process is reconstructed and written to `dump.bin` in the current directory, ready for static analysis.

### Write the dump to a named file

Give the extracted payload a proper name instead of the default.

```
python FridaExtract.py bad.exe --out_file unpacked.exe
```

Outcome: same extraction flow, but the rebuilt PE lands in `unpacked.exe` rather than `dump.bin`.

### Pass arguments the packed program expects

Some packers only reach the injection stage when their command line carries specific arguments.

```
python FridaExtract.py bad.exe --args password
```

Outcome: the sample is started with the extra arguments (comma-separated for several), then extraction proceeds as usual.

### Dump raw memory instead of a rebuilt PE

When automatic PE reconstruction fails, fall back to a raw dump of everything written to the child process.

```
python FridaExtract.py bad.exe --raw
```

Outcome: instead of a reconstructed PE, the raw memory segments written to the child are dumped in address order, for manual carving.

### Trace the hooked APIs verbosely

See each injection-related API call as it happens.

```
python FridaExtract.py bad.exe --verbose
```

Outcome: every hooked API is printed with its arguments as the sample runs, showing exactly where the payload is written and how execution is handed over.

*No corpus evidence — derived from upstream documentation.*
