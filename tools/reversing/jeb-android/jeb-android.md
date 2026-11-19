---
name: "jeb-android"
categories:
  - blackarch-reversing
version: "3.7.0.201909272058"
homepage: "https://www.pnfsoftware.com/jeb/android"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-16"
build-system: scripts
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# jeb-android

> JEB decompiler, Android variant: disassemble, decompile and debug Dalvik bytecode inside APKs.

## What it's for

JEB is a commercial reverse-engineering platform by PNF Software for disassembly, decompilation, debugging and analysis of code and documents, used through a desktop GUI client or a scriptable headless mode. This package is the Android variant: its DEXDEC pipeline decompiles Dalvik/DEX (including multi-dex APKs) back to Java, helps refactor obfuscated code produced by application protectors, reconstructs resources and obfuscated XML, and debugs Dalvik as well as native code. The BlackArch package installs PNF's demo build under `/opt/jeb-android` and exposes it as a `jeb-android` launcher that runs the product's `jeb_linux.sh` start-up script with all arguments forwarded. It ships no source, and headless scripting requires a registered Pro or Community Edition license; the sibling packages [[jeb-intel]], [[jeb-arm]], [[jeb-mips]] and [[jeb-webasm]] are the same product licensed for other architectures.

## Typical use cases

### Start the GUI client

Open the desktop client to analyze a target interactively.

```
jeb-android
```

Outcome: the JEB desktop client starts. Add an APK to the workspace, position the caret on code in the assembly view and press Tab to decompile that unit (MOD1+Tab offers decompilation with options).

### Run an analysis script headless

Execute a JEB Python script against the API without opening the GUI, e.g. for batch decompilation pipelines.

```
jeb-android -c --script=analyze.py -- input.apk
```

Outcome: JEB runs in console mode and executes the script, passing `input.apk` as an argument retrievable via the client context API; script output goes to the terminal. Requires a Pro or CE license.

### Check the license state

Print what license the installed build carries, from the terminal instead of the GUI.

```
jeb-android -c --license
```

Outcome: prints the license type, identifier and key information, the same data the GUI shows under Help, About.

*No corpus evidence — derived from upstream documentation.*
