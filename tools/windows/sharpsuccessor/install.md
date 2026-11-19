---
tool: "sharpsuccessor"
build-system: "windows"
upstream: "https://github.com/logangoins/SharpSuccessor"
recipe: "upstream"
license: "custom:unknown"
---

# Installing sharpsuccessor

> BadSuccessor dMSA weaponization POC (create + arm a dMSA to impersonate).

Source: hacktricks feed; upstream [`logangoins/SharpSuccessor`](https://github.com/logangoins/SharpSuccessor).

## Build

The README documents no build steps and the repo carries no LICENSE
file. It is a .NET project: clone and build on Windows with Visual
Studio/MSBuild (any recent .NET toolchain), then move the resulting
`SharpSuccessor.exe` to your staging share.

```bash
git clone https://github.com/logangoins/SharpSuccessor.git
```

No prebuilt releases are published upstream.

## On-target requirements

- Windows host joined to the target AD domain, running under the
  low-privileged user that holds `CreateChild` on the chosen OU and
  write access to the impersonation target.
- .NET Framework available to run the compiled exe.
- [[rubeus]] alongside it for the ticketing half of the attack
  (uploaded to the target with any file-transfer method).

## License

None stated in the repository — treat as unknown, author's copyright.

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[windows]] · [[sharpsuccessor]]
