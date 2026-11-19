---
tool: "sharpcollection"
build-system: "windows"
upstream: "https://github.com/Flangvik/SharpCollection"
recipe: "upstream"
license: "custom:unknown"
---

# Installing sharpcollection

> Nightly prebuilt binaries of common C# offensive tools, committed to the repo.

Source: pentest-book feed; upstream [`Flangvik/SharpCollection`](https://github.com/Flangvik/SharpCollection).

## Getting the binaries

The binaries are committed to the repository itself (no release
downloads). Full clone (~all frameworks):

```bash
git clone --depth 1 https://github.com/Flangvik/SharpCollection.git
```

Or fetch a single framework flavor with a sparse checkout:

```bash
git clone --depth 1 --filter=blob:none --sparse https://github.com/Flangvik/SharpCollection.git
cd SharpCollection
git sparse-checkout set NetFramework_4.7_Any
```

Binaries land in `NetFramework_<4.0|4.5|4.7>_<Any|x64|x86>/`. The
`Any` CPU flavors are the ones Cobalt Strike's `execute-assembly`
accepts.

## Requirements

A Windows host with the matching .NET Framework runtime (4.0/4.5/4.7)
for the binary you run.

## Opsec note

The upstream README explicitly warns to never deploy these binaries
blindly — review anything you ship in a decompiler
([dnSpyEx/dnSpy](https://github.com/dnSpyEx/dnSpy)) first. Unreviewed
drops are for labs, VMs and detection mapping only. Each binary is the
respective upstream tool under its own license; this repo carries no
license of its own.

## Not yet packaged

No distro packages this repository (checked 2026-11-13).

Backlinks: [[windows]] · [[sharpcollection]]
