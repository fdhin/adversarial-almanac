---
tool: "oida"
build-system: "python"
upstream: "https://github.com/f0rw4rd/oida"
recipe: "upstream"
license: "AGPL3"
---

# Installing oida

> One CLI to scan, fuzz and assess OT protocols (Modbus, OPC UA, S7, DNP3, BACnet, ...).

Source: hacktricks feed; upstream [`f0rw4rd/oida`](https://github.com/f0rw4rd/oida).

## Install from PyPI

The PyPI package is `oida-ics` (the name `oida` was taken); the command it installs is `oida`. Protocol stacks ship as extras:

```bash
pip install 'oida-ics[all]'
```

Install only what you need with e.g. `pip install 'oida-ics[modbus,opcua]'`. A bare `pip install oida-ics` has no protocol stacks at all.

## Docs

Full install guide and per-protocol guides at [getoida.dev](https://getoida.dev) (installation: getoida.dev/getting-started/installation).

## Warning

OIDA can crash devices, trigger physical actuator writes, and extract credentials. Use only against systems you own or have explicit written authorization to test; writes require the explicit `--confirm` flag.

Backlinks: [[scanner]] · [[oida]]
