---
tool: "clsdumper"
build-system: "python"
upstream: "https://github.com/TheQmaks/clsdumper"
recipe: "upstream"
license: "MIT"
---

# Installing clsdumper

> Frida-based Android DEX dumper with anti-Frida bypass and nine extraction strategies.

Source: hacktricks feed; upstream [`TheQmaks/clsdumper`](https://github.com/TheQmaks/clsdumper).

## Install from PyPI

```bash
pip install clsdumper
```

## Device requirements

- Rooted Android device with [frida-server](https://github.com/frida/frida/releases) running
- USB debugging enabled (or `--host` for a TCP connection to frida-server)
- Python 3.10+

## Building the agent from source

The Frida agent is pre-built and bundled as `clsdumper/frida/scripts/agent.js`. To modify it:

```bash
cd agent && npm install
npm run build
cd .. && pip install -e .
```

Backlinks: [[mobile]] · [[clsdumper]]
