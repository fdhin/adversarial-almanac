---
name: "hbctool"
categories:
  - mobile
homepage: "https://github.com/bongtrop/hbctool"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-13"
github-repo: "bongtrop/hbctool"
github-stars: 647
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "MIT"
---

# hbctool

> Disassembles and re-assembles Hermes bytecode — the compiled JavaScript of React Native Android apps — so the bundle can be read and patched like native code.

## What it's for

React Native apps that enable the Hermes engine ship their JavaScript as Hermes bytecode (HBC) instead of a readable `index.android.bundle`, which blocks the usual static analysis of app logic. `hbctool` converts that bytecode to a textual HASM disassembly and back, letting a pentester read string tables and function bodies, patch values or logic, and rebuild a working HBC file for a repackaged APK. It supports Hermes bytecode versions 59, 62, 74 and 76 — newer Hermes runtimes (found in current React Native releases) are out of scope, which is the main limitation to check before relying on it. On Android the target file normally lives at `assets/index.android.bundle` inside the APK.

## Typical use cases

### Disassemble the Hermes bundle

```
hbctool disasm index.android.bundle test_hasm
```

Outcome: writes a HASM disassembly of the app's Hermes bytecode into directory `test_hasm` (one file per function plus string tables) — the readable form of the app's compiled JavaScript.

### Patch and re-assemble

```
hbctool asm test_hasm index.android.bundle
```

Outcome: re-assembles the (edited) HASM directory back into a Hermes bytecode file that the app will load — edit a disassembled function or string, rebuild, then repackage/re-sign the APK with the patched bundle.

### Check supported bytecode version

```
hbctool --version
```

Outcome: prints the tool version; run `hbctool disasm` on the target file and check the reported HBC version against the supported set (59/62/74/76) before investing time in a patch.

*No corpus evidence — derived from upstream documentation.*
