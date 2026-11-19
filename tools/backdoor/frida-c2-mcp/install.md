---
tool: "frida-c2-mcp"
build-system: "python"
upstream: "https://github.com/s4dp4nd4/frida-c2-mcp"
recipe: "upstream"
version: "git (2026-03-18)"
license: "see repository"
---

# Installing frida-c2-mcp

> Agentic C2-style MCP server for Frida instrumentation on rooted Android and jailbroken iOS.

Source: kitploit directory; upstream [`s4dp4nd4/frida-c2-mcp`](https://github.com/s4dp4nd4/frida-c2-mcp).

## Prebuilt / releases

Check [upstream releases](https://github.com/s4dp4nd4/frida-c2-mcp/releases) for binaries.

## Any other Linux distro

```bash
git clone https://github.com/s4dp4nd4/frida-c2-mcp.git
cd frida-c2-mcp
python3 -m pip install --user -r requirements.txt  # or per README
```

## Not yet packaged

No distro packages this tool yet (checked 2026-11-07).

Backlinks: [[backdoor]] · [[frida-c2-mcp]]
