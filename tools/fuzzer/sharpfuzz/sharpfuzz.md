---
name: "sharpfuzz"
categories:
  - blackarch-fuzzer
version: "v2.2.0.r9.g7dde242"
homepage: "https://github.com/Metalnem/sharpfuzz"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-29"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Metalnem/sharpfuzz"
github-stars: 477
github-pushed: "2026-06-16"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# sharpfuzz

> AFL-based coverage-guided fuzz testing for .NET: instruments assemblies and reports unhandled exceptions as crashes.

## What it's for

SharpFuzz (Metalnem) brings afl-fuzz-style coverage-guided fuzzing to the .NET platform. It has two halves: the `sharpfuzz` command-line tool (the SharpFuzz.CommandLine .NET global tool) rewrites a .NET assembly in place with coverage-tracking instrumentation, and the SharpFuzz NuGet library provides `Fuzzer.OutOfProcess.Run`, which wraps the function under test — any unhandled exception it throws is reported to afl-fuzz as a crash. The classic targets are parsers and deserializers (JSON, markdown, PDF, protobuf — its trophy list includes Json.NET, Markdig, YamlDotNet, ImageSharp, and .NET's own CoreFX). It runs on Linux/macOS with AFL/AFL++ (a libFuzzer variant covers native Windows) and needs .NET 8+ for instrumentation. It fuzzes library code through a harness you write; it does not discover or drive GUI applications.

## Typical use cases

### Instrument a target assembly

Add AFL coverage hooks to the library under test.

```
sharpfuzz Jil.dll
```

Outcome: `Jil.dll` is rewritten in place with coverage instrumentation (success exits 0; a non-assembly file fails with an explanatory error). Optional trailing prefixes restrict instrumentation to a single class or namespace — e.g. `sharpfuzz BigLib.dll BigLib.Parsers` — omitting them instruments all types in the assembly.

### Run afl-fuzz against a harness

Drive the fuzz target with the classic AFL loop.

```
afl-fuzz -i Testcases -o Findings -t 5000 -x /usr/share/afl/dictionaries/json.dict \
  dotnet bin/Debug/net8.0/Fuzzing.dll
```

Outcome: coverage-guided fuzzing of the harness project, whose `Main` calls `Fuzzer.OutOfProcess.Run(stream => ...)` around the parser under test. Inputs that trigger unhandled exceptions land in `Findings/crashes`; replay each through the harness and dedupe by stack trace, since AFL overcounts unique crashes. The explicit `-t 5000` timeout matters — AFL's automatic timeout is too aggressive for managed code and yields false crashes.

### Drive a whole project with the wrapper script

Skip the manual instrument-and-wire steps.

```
pwsh scripts/fuzz.ps1 Jil.Fuzz.csproj -i Testcases -x /usr/local/share/afl/dictionaries/json.dict
```

Outcome: the bundled `fuzz.ps1` script performs the build/instrument/launch cycle for a .csproj whose entry point wraps the target in `Fuzzer.OutOfProcess.Run`, passing `-i` seeds and AFL flags straight through — the quickest path from a harness project to a running campaign; the `-x` dictionary markedly improves throughput for structured formats like JSON.

### Wire up the library in a harness

What the fuzzed entry point looks like.

```csharp
using System;
using System.IO;
using SharpFuzz;

public class Program
{
  public static void Main(string[] args)
  {
    Fuzzer.OutOfProcess.Run(stream =>
    {
      try { JSON.DeserializeDynamic(new StreamReader(stream)); }
      catch (DeserializationException) { }
    });
  }
}
```

Outcome: afl-fuzz feeds each mutated input to the lambda via `stream`; unexpected exceptions propagate and are recorded as crashes, while expected ones (`DeserializationException` for invalid JSON) are swallowed — this exception policy is what defines a finding, so catch only what is genuinely expected.

*No corpus evidence — derived from upstream documentation.*
