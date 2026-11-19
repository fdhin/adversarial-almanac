---
tool: "ilspy"
build-system: "dotnet"
upstream: "https://github.com/icsharpcode/ILSpy"
recipe: "upstream"
license: "MIT"
---

# Installing ilspy

> The open-source, cross-platform .NET assembly browser and decompiler (GUI) — install `ilspycmd` for the CLI.

Source: hacktricks feed; upstream [`icsharpcode/ILSpy`](https://github.com/icsharpcode/ILSpy).

## Desktop GUI (Windows / Linux / macOS)

Download the archive for your platform from the
[releases page](https://github.com/icsharpcode/ILSpy/releases) (e.g.
`ILSpy-linux-x64-Release.zip`), unpack and run the `ILSpy` executable.
A Microsoft Store package and a VS Code extension
([ilspy-vscode](https://marketplace.visualstudio.com/items?itemName=icsharpcode.ilspy-vscode))
are also published.

## CLI (ilspycmd) — any OS with the .NET SDK

```bash
dotnet tool install --global ilspycmd
```

Installing or updating needs .NET SDK 10.0.100+ (older SDKs cannot read the
pointer package); Windows x64 and Linux x64 get a self-contained Native AOT
build, other platforms a framework-dependent one.

## Build from source

```bash
git clone https://github.com/icsharpcode/ILSpy.git
cd ILSpy
git submodule update --init --recursive
dotnet build ILSpy.Desktop.slnf   # cross-platform desktop UI
dotnet build ILSpy.XPlat.slnf     # ilspycmd + PowerShell cmdlets only
```

Backlinks: [[decompiler]] · [[ilspy]]
