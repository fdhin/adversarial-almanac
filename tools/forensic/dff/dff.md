---
name: "dff"
gentoo-category: "app-forensics"
version: "1.3.0"
homepage: "http://tracker.digital-forensic.org/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "arxsys/dff"
github-stars: 317
github-pushed: "2020-02-13"
github-archived: false
github-issues: 21
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dff

> Digital Forensics Framework: a modular C++/Qt forensic workbench with a GUI and scriptable console for analyzing disk images and recovering files.

## What it's for

DFF (Digital Forensics Framework) is an open-source forensic framework developed by ArxSys and written in C++ with Python scripting. It opens disk dumps and evidence files through a modular architecture: file-system modules (FAT, NTFS, and others) build a virtual file system you can browse to inspect and recover files, including deleted ones. It provides both a Qt GUI — with an embedded command console — and a standalone console mode, and can be scripted through its Python API. Development is effectively dormant and the 1.3.0 release is from the Python 2 era, so building it on current systems takes legacy-dependency work.

## Typical use cases

### Start the graphical workbench

```
dff.py -g
```

Outcome: opens the Qt interface. Load media via the File(s) → Open evidence file(s) menu, right-click the dump in the Browser tab to apply a file-system module, then inspect and recover files in the Virtual File System tab.

### Drive the framework from the console

```
dff.py
```

Outcome: opens the interactive console at the `dff / >` prompt with Tab completion; `man <command>` prints built-in help for any console command.

### Add a local evidence folder and parse a FAT image

```
dff / > local --path /home/user/dumps --parent /
dff / > fat dumps/test.fat.dd
dff / > ls
```

Outcome: the `local` command maps a host directory into the virtual file system, `fat` applies the FAT module to the dump file, and `ls` lists the parsed virtual nodes — the recovered content can then be exported through the file modules.

*No corpus evidence — derived from upstream documentation.*
