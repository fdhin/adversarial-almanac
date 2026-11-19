---
name: "ghidra"
categories:
  - respond
version: "12.1.2-2"
homepage: "https://ghidra-sre.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-06-29"
build-system: java
meta-checked: "2026-11-09"
github-repo: "NationalSecurityAgency/ghidra"
github-stars: 98508
github-pushed: "2026-11-06"
github-archived: false
github-issues: 2190
github-match: "search"
gh-checked: "2026-11-07"
worth: 9.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3192
gh-activity-checked: "2026-11-09"
---

# ghidra

> Reverse-engineering workbench with a rich GUI, a code decompiler, and a scriptable headless mode for batch analysis.

## What it's for

Understanding unknown binaries: import a PE/ELF/Mach-O/firmware image, let auto-analysis map code and data, then read C-like decompiled source, walk cross-references and defined strings, rename and retype variables to recover intent, and patch or export the result. Analysis state is saved in a local project directory so work resumes where it left off, and the same engine runs without the GUI via `analyzeHeadless` for scripted batch triage and exports. It is an analysis workbench, not a debugger and not a hardened sandbox — analyze untrusted binaries in a disposable VM, and treat project archives (`.gpr`/zip) received from others as untrusted input, since the XML inside projects has historically been a working attack vector against the analyst's own machine.

## Typical use cases

### Launch the GUI and analyze a binary interactively

Open a project and import a sample for interactive reversing.

```
./ghidraRun
```

Outcome: the project window opens; File → New Project creates a local project, File → Import File loads the binary with a format/loader summary, and answering Yes to the analysis prompt runs full auto-analysis. The program then opens in the Code Browser with disassembly, the decompiler pane, and the symbol tree; for Windows PE files with symbols, File → Load PDB File restores function names before you start reading code.

### Analyze a single binary headlessly

Run the full import-and-analyze pipeline with no GUI, e.g. on a server or inside a triage script.

```
analyzeHeadless /cases/ghidra Triage -import target.exe
```

Outcome: the project is created on the fly at the given location (named `Triage`) if it does not already exist, `target.exe` is imported, auto-analysis runs, and the analyzed program is saved into the project. Progress and the import/analysis summary go to the console and a log file; a nonzero exit code flags import or analysis failure.

### Import and analyze a directory of samples

Process a whole batch of binaries in one run.

```
analyzeHeadless /cases/ghidra Batch -import /cases/samples -recursive
```

Outcome: every binary found under `/cases/samples` is imported and analyzed into the `Batch` project; add `-overwrite` to re-import a program that already exists instead of having it skipped.

### Run a script after analysis (postScript)

Attach scripted triage to the import pipeline instead of clicking through the GUI.

```
analyzeHeadless /cases/ghidra Triage -import target.exe -postScript FindPotentialDecompilerProblems.java
```

Outcome: after auto-analysis completes, the script runs inside the tool with full access to the program; `FindPotentialDecompilerProblems.java` ships with the decompiler and lists functions whose decompilation looks suspect, such as parameters it could not recover. For custom scripts pass `-scriptPath /cases/scripts` before `-postScript myscript.py`; tokens after the script name are handed to the script as arguments.

### Re-run scripts on an already-analyzed program

Iterate on scripts without paying for analysis twice.

```
analyzeHeadless /cases/ghidra Triage -process target.exe -noanalysis -readOnly -postScript myscript.py
```

Outcome: the saved program is opened and the script runs against the existing analysis; `-noanalysis` skips re-analysis entirely and `-readOnly` writes nothing back to the project — safe for repeated scripted queries over a stable corpus.

### Export decompiled source for a binary

Produce C-like source for review, grepping, or diffing outside the tool.

```
analyzeHeadless /cases/ghidra Triage -import target.exe -scriptPath /cases/scripts -postScript ExportDecompiledSource.py
```

Outcome: a custom postScript walks all functions through the decompiler API and writes source files to disk. The GUI equivalent is File → Export Program with format C/C++, which writes a single decompiled `.cpp` with no scripting at all.

### Patch a binary and export the patched copy

Apply the byte-level fix found during analysis and produce a runnable modified binary.

```
./ghidraRun
```

Outcome: in the Code Browser, patch instructions or data bytes in the listing via the context-menu patch commands, then File → Export Program in the original format (ELF, PE, ...) writes the patched binary to disk; deploy that exported file in place of the original to dynamically test the fix.
