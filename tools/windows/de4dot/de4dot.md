---
name: "de4dot"
categories:
  - blackarch-windows
version: "2090.b7d5728"
homepage: "https://github.com/de4dot/de4dot"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-13"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "de4dot/de4dot"
github-stars: 7425
github-pushed: "2020-08-29"
github-archived: true
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# de4dot

> .NET deobfuscator and unpacker that restores obfuscated assemblies to analyzable form.

## What it's for

de4dot cleans obfuscated .NET assemblies so a decompiler can read them: it decrypts strings, resources and constants, inlines proxy delegate methods, undoes control-flow obfuscation, renames obfuscated symbols to readable ones, and unwraps packers and obfuscators (SmartAssembly, Dotfuscator, .NET Reactor, Eazfuscator.NET, Babel.NET, CryptoObfuscator and many more). The standard workflow is to run it on a packed .NET executable first, then load the cleaned output into a decompiler/debugger such as dnSpy. It does not recover original symbol names (they are not stored in the obfuscated assembly), and some decryption backends execute parts of the input assembly, so run untrusted samples in a sandbox. The original project is archived; the de4dotEx fork carries the same CLI forward with active maintenance.

## Typical use cases

### Deobfuscate a single assembly

Strip obfuscation from one file.

```
de4dot file.exe
```

Outcome: detects the obfuscator, writes the cleaned assembly next to the input as `file-cleaned.exe`, and prints what it detected and removed. Load the cleaned file in a .NET decompiler to read the restored source.

### Detect the obfuscator only

Identify what protected a file without modifying it.

```
de4dot -d file1.dll file2.dll
```

Outcome: prints the detected obfuscator for each file (or "Unknown obfuscator"); nothing is written.

### Batch-deobfuscate a directory tree

Clean every supported assembly of an application together.

```
de4dot -r ./input -ru -ro ./output
```

Outcome: recursively finds .NET assemblies under `./input`, deobfuscates the recognized ones (`-ru` skips unknown/unobfuscated files) and writes results into `./output`. Processing all assemblies of an application at once keeps cross-assembly references valid after renaming.

### Force a specific obfuscator

Override detection when it guesses wrong or fails.

```
de4dot file1.dll -p sa
```

Outcome: treats the file as SmartAssembly-obfuscated (`sa`; use `un` for an unknown/unsupported obfuscator) and applies that deobfuscator's logic.

### Preserve metadata tokens

Keep token references stable for tooling that logs them.

```
de4dot --preserve-tokens file1.dll
```

Outcome: writes a cleaned assembly whose metadata tokens (and #US string heap) match the original, for correlating with error logs or config entries that reference tokens from the obfuscated build.
