---
name: "simplify"
categories:
  - blackarch-mobile
version: "1.3.0"
homepage: "https://github.com/CalebFenton/simplify"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-05-04"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "CalebFenton/simplify"
github-stars: 4677
github-pushed: "2022-04-30"
github-archived: false
github-issues: 33
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# simplify

> Generic Android deobfuscator: virtually executes Dalvik code and rewrites it into readable equivalents.

## What it's for

Simplify (by Caleb Fenton) takes an APK, DEX file or smali directory and
deobfuscates it without knowing which obfuscator produced the input. Its
sandboxed Dalvik virtual machine (smalivm) executes each method, taking both
arms of branches on unknown values, and then applies generic optimizations —
constant propagation, dead code removal, unreflection, peephole rewriting —
repeatedly until the method stops changing. String-decryption calls collapse
into plain literals and dead obfuscation scaffolding disappears, which makes
subsequent decompiler output far more readable. It does not rename classes
or methods; a rewritten APK must be re-signed (see [[sign]]) before it will
install, and multi-DEX APKs (with `classes2.dex`) are rejected.

## Typical use cases

### Deobfuscate an APK

Simplify a whole sample while scoping execution to the classes of interest.

```
simplify -it 'com/example/' -et 'BuildConfig' -o simplified.apk target.apk
```

Outcome: virtually executes the included classes (`-it` limits to names
matching the regex, `-et` excludes matches applied after the include), writes
the rewritten APK to `-o`, and ends with a count of optimized, failed and
skipped methods; omitted `-o` writes alongside the input as
`target_simple.apk`.

### Deobfuscate a single DEX or smali directory

Work on extracted artifacts rather than a full APK.

```
simplify -o simplified.dex classes.dex
```

Outcome: reads the DEX (a smali directory works the same way) and writes the
optimized DEX to the given path; the exit status is 1 if any method fails to
execute or an optimizer edit is rejected, 0 otherwise.

### Keep going past hard methods

Let a large obfuscated sample finish instead of aborting.

```
simplify --ignore-errors --max-address-visits 20000 --max-call-depth 100 target.apk
```

Outcome: `--ignore-errors` skips methods that fail (exit status 0 even with
skips), and raising the analysis limits stops smalivm from giving up on
methods with big loops (`--max-address-visits`, default 10000), deep call
chains (`--max-call-depth`, default 50) or long bodies (`--max-method-visits`,
default 1000000); `-v`/`-vv` add the verbose logs wanted for bug reports.

*No corpus evidence — derived from upstream documentation.*
