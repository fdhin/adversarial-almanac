---
name: "clsdumper"
categories:
  - mobile
homepage: "https://github.com/TheQmaks/clsdumper"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
license: MIT
meta-checked: "2026-11-13"
github-repo: "TheQmaks/clsdumper"
github-match: "exact"
github-stars: 71
gh-checked: "2026-11-13"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# clsdumper

> Frida-based Android DEX dumper — extracts every class file from a running app through nine strategies, past anti-Frida defenses, packs and dynamic loaders included.

## What it's for

clsdumper (PyPI, MIT) hooks a running — or freshly spawned — Android application and dumps every DEX file it can find, using nine extraction strategies across native and Java phases: an ART runtime walk (Runtime → ClassLinker → DexFile structs), hooking `DexFile::OpenCommon`, scanning readable memory for DEX magic bytes, reading ClassLoader `mCookie` via reflection, monitoring `loadClass`/`DexClassLoader`, parsing mapped `.oat`/`.vdex` files, the high-coverage `fart_dump` (DefineClass hook plus `class_table_` walk), and hooking OatDexFile constructors. That spread defeats the usual evasion: packed or encrypted DEX, dynamically loaded code, and apps with anti-Frida protections — clsdumper blocks signal-handler registration, filters `/proc/self/maps` reads and neutralizes watchdog threads before extracting, and its native strategies run outside `Java.perform()`, so they keep working when the Java bridge dies on heavily protected apps. Requires a rooted device with frida-server running. Dumps are deduplicated (djb2 on the agent, SHA-256 on the host) and optionally decompiled to individual smali classes via androguard.

## Typical use cases

### Dump a running app

Attach to what's already executing.

```
clsdumper com.example.app
```

Outcome: attaches over USB to the running process, runs the default strategy set, and writes every discovered DEX to `./dump_com.example.app/dex/` plus a `metadata.json` recording which strategy found each file — late-loaded and decrypted classes are captured because extraction happens at runtime, not from the APK on disk.

### Spawn the app yourself

Catch decryption at load time.

```
clsdumper com.example.app --spawn --extract-classes
```

Outcome: starts the app under instrumentation (guaranteeing hooks are in place before any packer runs), dumps the DEX set, and additionally decompiles each DEX into individual `.smali` class files under `dump_com.example.app/classes/` via androguard.

### Pick specific strategies

Trade coverage for speed or stability.

```
clsdumper com.example.app --strategies fart_dump,oat_extract
```

Outcome: runs only the named strategies — `fart_dump` has the best coverage; the default is all strategies except `mmap_hook`, which is excluded for performance when combined with the other hooks.

### Target a device over the network

No USB, or a remote test rig.

```
clsdumper com.example.app --host 198.51.100.10
```

Outcome: connects to the frida-server listening at that address over TCP instead of USB; find targets first with `clsdumper --list-apps` (installed applications) or `clsdumper --list` (running processes) — a bare PID also works as the target.

### Catch compact DEX too

Older and optimized formats.

```
clsdumper com.example.app --deep-scan
```

Outcome: the slower, deeper memory scan also picks up CDEX (compact DEX) files that the standard scan skips — use when counts look low for a big app.

No corpus evidence — derived from upstream documentation.
