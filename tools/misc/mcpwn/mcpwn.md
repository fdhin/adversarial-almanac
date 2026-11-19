---
name: "mcpwn"
categories:
  - misc
version: "1.3.0-0parrot2"
homepage: "https://github.com/ParrotSec/mcpwn"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: go
meta-checked: "2026-11-09"
github-repo: "ParrotSec/mcpwn"
github-stars: 12
github-pushed: "2026-03-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 40
gh-activity-checked: "2026-11-09"
---

# mcpwn

> MCP server that lets an LLM client drive local security tools (nmap, ffuf, sqlmap, ...) through a YAML-defined toolset.

## What it's for

mcpwn (ParrotSec) is a Model Context Protocol server, written in Go, that exposes local security tools to LLM clients as callable tools. It ships definitions for seven tools out of the box — nmap, gobuster, ffuf, httpx, sqlmap, msfvenom and msfconsole — and further tools are added by editing a `mcpwn.yaml` file (name, description, command, typed arguments) rather than writing code. By default every invocation runs inside an ephemeral Docker container (`--cap-drop=ALL --cap-add=NET_RAW --rm`, no host volumes or sockets mounted); without Docker the command runs directly via Go's `exec.Command`, which never involves a shell, so metacharacters in LLM-supplied arguments cannot chain commands. The server speaks MCP over stdio, which MCP-capable clients attach to natively. It deliberately grants an LLM real execution power on your machine — run it only in an environment you control.

## Typical use cases

### Start the server standalone

Check that the configuration loads before wiring up a client.

```
mcpwn
```

Outcome: the server starts, speaks MCP on stdin/stdout and logs to stderr; on connect a client sees the tools defined in `mcpwn.yaml` next to the executable, or `/etc/mcpwn/mcpwn.yaml` for system-wide installs.

### Register it with Claude Code

Make the toolset available inside Claude sessions.

```
claude mcp add --transport stdio mcpwn -- /usr/bin/mcpwn
```

Outcome: the server is registered as an MCP backend; new Claude sessions list its security tools and can invoke, for example, an nmap scan with structured arguments, with the tool's output returned to the model.

### Register it with Gemini CLI

Do the same for the Gemini CLI.

```
gemini mcp add mcpwn /usr/bin/mcpwn
```

Outcome: `gemini` sessions can call the registered tools; start `gemini` and ask it to run one of the exposed security tools to have it dispatched through mcpwn.

### Expose an additional tool

Add a definition to the configuration file.

```
# mcpwn.yaml
tools:
  - name: "nmap_scan"
    description: "Nmap is a free and open source utility for network discovery and security auditing."
    command: "nmap"
    image: "parrotsec/nmap"
    args:
      - name: "target"
        description: "Target IP/Domain"
        required: true
        positional: true
      - name: "aggressive"
        description: "Aggressive scan (-A)"
        flag: "-A"
        type: "boolean"
```

Outcome: after restarting mcpwn the tool appears to clients with its typed arguments; because `image` is set, invocations run in a temporary Docker container that is destroyed on completion. Omit `image` to run the locally installed binary directly, and use a free-form `extra_args` argument to pass arbitrary CLI flags.

*No corpus evidence — derived from upstream documentation.*
