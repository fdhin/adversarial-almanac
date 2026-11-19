---
name: "ffdec"
categories:
  - blackarch-decompiler
version: "26.2.1"
homepage: "https://github.com/jindrapetrik/jpexs-decompiler"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-09-13"
build-system: java
license: GPL-3.0-or-later
aliases: ["jpexs-decompiler"]
meta-checked: "2026-11-09"
github-repo: "jindrapetrik/jpexs-decompiler"
github-stars: 6014
github-pushed: "2026-11-07"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 736
gh-activity-checked: "2026-11-09"
---

# ffdec

> JPEXS Free Flash Decompiler: SWF decompiler, editor and resource extractor.

## What it's for

Working with Flash SWF content: decompile ActionScript 2/3 back to source, export every embedded resource type (images, shapes, sprites, sounds, fonts, frames, binary data, texts) to standard formats, convert a whole SWF into an FLA or XFL project, and patch SWFs in place by replacing scripts, images, sounds or other tags. It offers both a GUI and a complete headless command-line interface — `ffdec` on Linux — so batch extraction and patching can run without a display. Export formats are adjustable per item type with the `-format` pre-option (e.g. `script:pcode` emits ActionScript P-code instead of source). Written by Jindra Petřík (JPEXS), GPL-3.0-or-later, runs anywhere a Java runtime is available.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Open an SWF in the GUI

Inspect a file interactively.

```
ffdec /home/user/analysis/target.swf
```

Outcome: the decompiler GUI starts with the SWF loaded — scripts are browsable as decompiled ActionScript, and all tags and resources appear in the tree; several files can be given to open them together.

### Export all ActionScript source

Recover the scripts of an SWF headlessly.

```
ffdec -export script /home/user/swf-out /home/user/analysis/target.swf
```

Outcome: the decompiled ActionScript source tree is written under /home/user/swf-out, one file per script; passing a directory as the input exports every SWF found in it.

### Export only selected AS3 classes

Narrow the export to classes of interest.

```
ffdec -selectclass com.example.AuthService,net.company.+ -export script /home/user/swf-out /home/user/analysis/target.swf
```

Outcome: only the named AS3 classes are exported — `com.example.AuthService` itself plus every class under package `net.company` (`+` is a package wildcard, `++` includes subpackages; AS3 only, no spaces after commas).

### List the scripts inside an SWF

Enumerate what an SWF contains before exporting.

```
ffdec -dumpAS3 /home/user/analysis/target.swf
```

Outcome: prints the list of AS3 scripts (the class names that `-selectclass` and `-replace` expect) to the console; `-dumpAS2` does the same for AS1/2 pscripts and `-dumpSWF` dumps the raw tag list.

### Convert an SWF to an FLA project

Move the whole file into an editor project.

```
ffdec -export fla /home/user/fla-out /home/user/analysis/target.swf
```

Outcome: a compressed FLA project is written under /home/user/fla-out, openable in Adobe Animate/Flash; item type `xfl` produces the uncompressed directory form instead.

### Replace a script and write a patched SWF

Recompile an edited script back into the file.

```
ffdec -replace /home/user/analysis/target.swf /home/user/analysis/patched.swf com.example.AuthService /home/user/edits/AuthService.as
```

Outcome: the AS3 script com.example.AuthService is recompiled from the edited source file and a patched SWF is written to patched.swf; the same command form replaces images, sounds, shapes, texts, fonts or binaryData when given their character id instead of a script name.
