---
tool: "decepticon"
build-system: "python"
upstream: "https://github.com/BitterSecurity/Decepticon"
recipe: "upstream"
source: "https://decepticon.red/install"
license: "Apache-2.0"
---

# Installing decepticon

> Prerequisites: Docker and Docker Compose v2. Runs on macOS (Apple Silicon + Intel), Linux (amd64 + arm64), Windows native via PowerShell or WSL2 (Ubuntu/Kali). A hosted version exists at app.decepticon.red if you don't want to self-host.

## macOS / Linux / WSL2

```bash
curl -fsSL https://decepticon.red/install | bash
decepticon onboard   # interactive setup wizard
decepticon           # start the stack + terminal CLI
```

## Windows (PowerShell, native)

```powershell
irm https://decepticon.red/install.ps1 | iex
decepticon onboard
decepticon
```

## As a library (pip)

```bash
pip install decepticon              # core SDK
pip install "decepticon[neo4j]"     # + knowledge-graph attack-chain tools
```

The pip package is a client SDK — agent factories, middleware, tools and skills; LLM calls and sandbox execution still route to runtime services over HTTP (`DECEPTICON_LLM__PROXY_URL`, `SANDBOX_URL`), so use the Docker stack or point those URLs at your own equivalents.

## Development

```bash
git clone https://github.com/BitterSecurity/Decepticon.git
cd Decepticon
make dogfood    # full UX (launcher → onboard → CLI) on local code
make dev        # backend hot-reload (compose watch)
```

Backlinks: [[ai]] · [[decepticon]]
