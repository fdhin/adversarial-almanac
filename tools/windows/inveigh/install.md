---
tool: "inveigh"
build-system: "dotnet"
upstream: "https://github.com/Kevin-Robertson/Inveigh"
recipe: "upstream"
license: "see repository"
---

# Installing inveigh

> Windows/mitm password capture: spoof LLMNR/NBT-NS/DNS/mDNS/DHCPv6 and harvest NetNTLM hashes through SMB, HTTP, HTTPS, WebDAV, LDAP and proxy-auth listeners.

Source: hacktricks feed; upstream [`Kevin-Robertson/Inveigh`](https://github.com/Kevin-Robertson/Inveigh).

## Prebuilt / releases

Check [upstream releases](https://github.com/Kevin-Robertson/Inveigh/releases) for compiled drops (the C# version also builds as `Inveigh.dll`, runnable with `dotnet Inveigh.dll`).

## Build from source (Windows)

Open the solution in Visual Studio (the SDK-style project targets .NET
3.5/4.6.2/6.0) and build Release. Default parameter values are set at the
top of `Program.cs` — review them before compiling. Requires admin/elevated
rights at run time for the raw-socket spoofers.

## Linux / macOS single-file build

```bash
git clone https://github.com/Kevin-Robertson/Inveigh.git
cd Inveigh
dotnet publish --self-contained=true -p:PublishSingleFile=true -r linux-x64 -p:AssemblyName=inveigh
```

On non-Windows builds the packet sniffer is disabled; the SMB listener
covers port 445 when free.

## Legacy PowerShell version

The frozen PowerShell 1.506 edition lives in the same repository and its
docs on the [wiki](https://github.com/Kevin-Robertson/Inveigh/wiki); import
the .ps1 in a session and invoke `Invoke-Inveigh`.

## Not yet packaged

No distro repos package this tool (checked 2026-11-13).

Backlinks: [[windows]] · [[inveigh]]
