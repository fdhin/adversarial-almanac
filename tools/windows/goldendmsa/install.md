---
tool: "goldendmsa"
build-system: "windows"
upstream: "https://github.com/Semperis/GoldenDMSA"
recipe: "upstream"
license: "BSD-3-Clause"
---

# Installing goldendmsa

> Offline dMSA/gMSA password forgery from a stolen KDS root key.

Source: hacktricks feed; upstream [`Semperis/GoldenDMSA`](https://github.com/Semperis/GoldenDMSA).

## Build from source

The upstream README documents no prebuilt releases; build from source:

- Compile with platform target **x64**.
- Tested against **.NET Framework 4.7.2**.

The project is a C# source tree; the README gives no step-by-step, only
the requirements above (x64 platform target, .NET Framework 4.7.2).

## Deploying to the analysis host

When copying the tool's executable to the remote machine, also copy the
`CommandLine.dll` that sits in the `GoldendMSA` folder — the CLI parsing
depends on it and the exe fails without it.

## Requirements

A Windows host with .NET Framework 4.7.2+; domain credentials matching
the phase you are running (Enterprise Admins or SYSTEM on a DC for KDS
root key extraction, any authenticated user for LDAP/RID enumeration).

## License

BSD 3-Clause (stated in the README; educational use framing by
Semperis).

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[windows]] · [[goldendmsa]]
