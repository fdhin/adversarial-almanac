---
tool: "loki-c2"
build-system: "node"
upstream: "https://github.com/boku7/Loki"
recipe: "upstream"
license: "custom:BSL-1.1"
---

# Installing loki-c2

> Stage-1 C2 that script-jacks signed Electron apps; Azure Storage Blob channel.

Source: hacktricks feed; upstream [`boku7/Loki`](https://github.com/boku7/Loki).

## Operator box (payload generation + client)

Requires Node.js. Clone and build the obfuscated agent payload:

```bash
git clone https://github.com/boku7/Loki.git && cd Loki
npm install --save-dev javascript-obfuscator
node create_agent_payload.js
```

`create_agent_payload.js` prompts for the Azure storage account URL and
SAS token, then writes the obfuscated payload to `./app/`.

## Azure Storage Blob channel

Create a storage account and a SAS token protecting it — upstream ships
step-by-step guides for both the Azure Portal and the Azure CLI under
`docs/azure/` in the repo.

## Client

The GUI client is compiled per platform; upstream guides live under
`docs/compile/client/{windows,macos,linux}.md`. Build it on the platform
and architecture you operate from.

## Agents

No agent compilation is needed when backdooring Electron apps — the
generated `./app/` payload is plain JavaScript and runs under the target
app's own Electron. Agent compile guides (`docs/compile/agent/...`) are
for development only and not recommended for operations.

## Detection awareness

Upstream documents IOCs and SIGMA rules (Electron apps beaconing to
`*.blob.core.windows.net`, `resources/app` directories created/modified
outside installers) — review `Detection Guidance` in the README before
operating.

## License

Business Source License 1.1: non-commercial use only; commercial use
requires the author's permission. Converts to Apache 2.0 on 2030-04-03.

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[backdoor]] · [[loki-c2]]
