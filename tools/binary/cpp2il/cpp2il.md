---
name: "cpp2il"
categories:
  - blackarch-binary
version: "2022.0.7.r17.g20ccab2"
homepage: "https://github.com/SamboyCoding/Cpp2IL"
packaged-by: [blackarch]
blackarch-builddate: "2023-11-12"
build-system: other
license: MIT
meta-checked: "2026-11-09"
github-repo: "SamboyCoding/Cpp2IL"
github-stars: 3088
github-pushed: "2026-11-05"
github-archived: false
github-issues: 54
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 177
gh-activity-checked: "2026-11-09"
---

# cpp2il

> Recovers .NET metadata from Unity IL2CPP builds as DummyDLL assemblies and analysis dumps.

## What it's for

Unity games built with the IL2CPP scripting backend compile their C# code to
native machine code, leaving only files like `global-metadata.dat` as a trace
of the original managed assemblies. Cpp2IL parses that metadata (through
LibCpp2IL) and reconstructs usable artifacts from it — DummyDLL assemblies
that decompile cleanly in dnSpy or ILSpy, plus dump formats — so game logic,
hardcoded secrets or cheat-relevant classes can be read like ordinary .NET
code. It does not yet regenerate true IL method bodies: output is a
pseudo-reconstruction, and the analysis pass was being rewritten around the
2022.x releases. Windows x86/x64 game folders are the best-supported input;
single-file APKs are accepted directly (Cpp2IL 2021.4.0 and later).

## Typical use cases

### Recover managed assemblies from a Unity IL2CPP game folder

```
cpp2il --game-path=/path/to/game
```

Outcome: Cpp2IL auto-detects the Unity version, locates the IL2CPP binary
and metadata, and writes its output into a `cpp2il_out` folder in the
working directory; the DummyDLLs inside open directly in a .NET decompiler.

### Point the extractor at the right executable

```
cpp2il --game-path=/path/to/game --exe-name=Game
```

Outcome: skips exe auto-detection (useful when the game folder ships several
executables, e.g. launcher plus crash handler) and processes that specific
game executable; output lands in `cpp2il_out` as above.

### Pull game data straight out of an APK

```
cpp2il --game-path=/path/to/game.apk
```

Outcome: Cpp2IL extracts the IL2CPP binary and metadata it needs from a
single-file APK on the fly (2021.4.0+), so no manual unpacking step is
needed before dumping.

### Choose the output format and destination explicitly

```
cpp2il --game-path=/path/to/game --output-as=dummydll --output-to=cpp2il_out
```

Outcome: selects the output format by name and redirects the output root
directory; the available formats and processing layers can be enumerated
first with `--list-output-formats` and `--list-processors` respectively.

*No corpus evidence — derived from upstream documentation.*
