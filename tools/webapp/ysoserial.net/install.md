---
tool: "ysoserial.net"
build-system: "dotnet"
upstream: "https://github.com/pwntester/ysoserial.net"
recipe: "upstream"
license: "MIT"
---

# Installing ysoserial.net

> ysoserial.net — a collection of .NET gadget chains and utilities for exploiting unsafe deserialization in .NET applications.

Source: hacktricks feed; upstream [`pwntester/ysoserial.net`](https://github.com/pwntester/ysoserial.net). Not related to frohoff's Java `ysoserial` despite the shared name.

## Prebuilt / releases

```
# latest builds: https://github.com/pwntester/ysoserial.net/actions  (upstream-recommended)
# tagged releases: https://github.com/pwntester/ysoserial.net/releases
```

Outcome: download the compiled `ysoserial.exe` artifact; it runs on Windows with the .NET Framework installed and on Linux under mono.

## Build from source (Windows)

```
git clone https://github.com/pwntester/ysoserial.net
cd ysoserial.net
nuget restore ysoserial.sln
msbuild ysoserial.sln -p:Configuration=Release
.\ysoserial\bin\Release\ysoserial.exe -h
```

Outcome: Visual Studio 2022 with the .NET desktop workload plus NuGet CLI and msbuild on PATH produce `ysoserial.exe`; the `-h` run prints the usage banner confirming the build.

## Build from source (Linux via Nix)

```
git clone https://github.com/pwntester/ysoserial.net
cd ysoserial.net
nix develop --impure
nuget restore ysoserial.sln
dotnet build ysoserial.sln --configuration=Release
mono ysoserial/bin/Release/ysoserial.exe -h
```

Outcome: the repo's flake provides the .NET SDK environment; the built exe runs under mono on Linux.

## Not packaged — and a namesake warning

No distro packages this tool (checked 2026-11-13). BlackArch's `ysoserial` package builds **frohoff/ysoserial**, the Java original — it is not this project; do not install it expecting the .NET generator.

Backlinks: [[webapp]] · [[ysoserial.net]]
