---
name: "sharpcollection"
categories:
  - windows
homepage: "https://github.com/Flangvik/SharpCollection"
origin: "pentest-book"
recipe: "upstream"
build-system: windows
meta-checked: "2026-11-13"
github-repo: "Flangvik/SharpCollection"
github-stars: 3005
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "custom:unknown"
---

# sharpcollection

> Nightly prebuilt binaries of ~90 common C# offensive tools, cross-compiled for .NET 4.0/4.5/4.7 and committed straight to the repo.

## What it's for

SharpCollection (by Flangvik) is a distribution channel, not a tool
with its own CLI: an Azure DevOps pipeline checks the master branches
of a curated list of upstream C# offensive projects every night at
03:00, rebuilds each one, and commits the resulting binaries into this
repository under `NetFramework_<4.0|4.5|4.7>_<Any|x64|x86>/` folders —
so a plain `git clone` yields current-day builds of Rubeus, Seatbelt,
SharpHound, SharpDPAPI, Whisker, SweetPotato, winPEAS and many more
without installing a compiler. The `Any` (Any CPU) builds are what
Cobalt Strike's `execute-assembly` accepts. It adds no functionality of
its own — usage lives in each member tool's own note (e.g.
[[rubeus]], [[seatbelt]]). The upstream README is blunt about opsec:
never deploy these binaries blindly; review them in a decompiler
(dnSpyEx/dnSpy) first, and reserve unreviewed drops for labs, VMs and
detection mapping.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Pull tonight's builds with a sparse checkout

Fetch one framework flavor without the whole binary history.

```
git clone --depth 1 --filter=blob:none --sparse https://github.com/Flangvik/SharpCollection.git
cd SharpCollection
git sparse-checkout set NetFramework_4.7_Any
```

Outcome: the working tree contains only the .NET 4.7 Any CPU folder
with the latest nightly build of every actively compiled tool
(`Rubeus.exe`, `Seatbelt.exe`, `SharpHound.exe`, ...); the folder's
last commit date is the build date.

### List the currently built catalog

See which tools the pipeline is shipping tonight.

```
ls NetFramework_4.0_Any/
```

Outcome: one `.exe` per actively built tool for the .NET 4.0 Any CPU
target; tools absent from the folder are not being built (compare with
the build-status table in the upstream README).

### Check which .NET targets a tool ships

Pick the right binary for the target host's framework.

```
ls -d NetFramework_*/ | tr '\n' ' '
```

Outcome: prints the nine flavor folders (`NetFramework_4.0_Any`,
`NetFramework_4.5_x64`, `NetFramework_4.7_x86`, ...) — pick the row
matching the target's installed .NET Framework and architecture, with
`Any` flavors for `execute-assembly`.

### Record a binary's provenance before use

Attribute what you deployed, per the README's review-first rule.

```
sha256sum NetFramework_4.7_Any/Rubeus.exe
git log -1 --format='%ci %s' -- NetFramework_4.7_Any/Rubeus.exe
```

Outcome: prints the SHA-256 hash of the exact nightly build plus the
commit that produced it — pin these alongside engagement notes so the
binary can be attributed and re-verified later.
