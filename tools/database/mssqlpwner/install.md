---
tool: "mssqlpwner"
build-system: "python"
upstream: "https://github.com/ScorpionesLabs/MSSqlPwner"
recipe: "upstream"
license: "custom:unknown"
---

# Installing mssqlpwner

> MSSqlPwner — impacket-based tool for pentesting MSSQL servers: linked-server chains, command execution, NTLM relay, ADSI password retrieval.

Source: hacktricks feed; upstream [`ScorpionesLabs/MSSqlPwner`](https://github.com/ScorpionesLabs/MSSqlPwner).

## Install via pip (from git)

```
pip install 'mssqlpwner@git+https://github.com/ScorpionesLabs/MSSqlPwner.git'
```

Outcome: installs the `mssqlpwner` entry point and its impacket dependency.

## Install via pipx (isolated)

```
pipx install 'git+https://github.com/ScorpionesLabs/MSSqlPwner.git'
```

Outcome: same, in an isolated venv so impacket version conflicts with other tools cannot break either side; run `mssqlpwner -h` afterwards to confirm the module list prints.

## Notes

- No distro packages this tool (checked 2026-11-13).
- Upstream ships no license file in the repository (checked 2026-11-13).
- Kerberos ticket authentication (`brute -tl`) expects tickets in SPN form, e.g. `MSSQLSvc/host.corp.example.com:1433`.

Backlinks: [[database]] · [[mssqlpwner]]
